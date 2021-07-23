# sample-popeye-policy

This is sample [Red Light Green Light](https://rl.gl) (rlgl) policy
for analyzing [Popeye](https://popeyecli.io/) k8s sanitization
reports.  Red Light Green Light makes it easy to build automation
based on the application of custom defined policy against a report
from Popeye.

```shell
$ popeye -A -o html > popeye.html
$ rlgl login https://rl.gl
rlgl Missing API key.  Generate a new one at https://rl.gl/get-api-key
```

At this point, click on the link above to generate an API key, and continue below.

```shell
$ rlgl login --key MY_API_KEY https://rl.gl
$ export ID=$(rlgl start)
$ rlgl evaluate --id=$ID --policy=https://github.com/atgreen/sample-popeye-policy popeye.html
RED: https://rl.gl/doc?id=RLGL-FO8W7G4J (sha3/256: 6f211abd124bc15f1bf4537f8956a131d0289fc451eabb4cd29ee807822e3f39)
$ echo $?
1
```

This tells us that the popeye sanitization report doesn't meet the
policy we've defined in out git repo.  Click on the link to explore
the analysis, which includes direct links to policy commits in github,
and a link to the original report.

The analysis report was signed by both the rlgl server and your
client, and the server's document signature was uploaded to the
transparency log managed by the [sigstore](https://sigstore.dev)
project.

You can verify all of the signatures and the
[rekor](https://github.com/sigstore/rekor) log entry like so:

```shell
$ rlgl verify RLGL-FO8W7G4J | RLGL_CLIENT_PUBKEY=~/.config/rlgl/public_key.pem sh
Checking document signature: Verified OK
Checking client signature  : Verified OK
Searching for sigstore record:
LogID: c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d
Index: 7860
IntegratedTime: 2021-07-23T21:57:13Z
UUID: 099b342f36f3c0515a65e040100ce560b37616d2ab35a6adad60b52c62d0aa70
Body: {
  "RekordObj": {
    "data": {
      "hash": {
        "algorithm": "sha256",
        "value": "5475aff643cd22cd14633873e07621b8709f00ec0406653d34537eb371042306"
      }
    },
    "signature": {
      "content": "MGYCMQDnseFujxxRUuXN/Dh1qaKZt2nr9m3El7AwNRKVSpfrkWX6xcUXPZe7q0JA15LfOtQCMQDoIZLjTAxthjEVFzxdNu8HbFo/g8Ie9dsNQI3WBf0bxPOWHKYUiqFAeL3uCwV9gHM=",
      "format": "x509",
      "publicKey": {
        "content": "LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUhZd0VBWUhLb1pJemowQ0FRWUZLNEVFQUNJRFlnQUU4ZG8rQVFwbm5tanBwK1J1Y05tTy8zN04xVWpGNzZNZwpXd01Jcm1odlZvTjExajZXL0krSitQdk5NbDZiWHdvQnh0dk53V3dLbzFSdEZ3dGFXMWpWZnNCNEV6SkErb05PCkdEUDlNTmdCQW5uN3JiKzgrTm1XUW1IUllQeEJtbmFJCi0tLS0tRU5EIFBVQkxJQyBLRVktLS0tLQo="
      }
    }
  }
}
```

These digital signatures, combined with the sigstore public
transparency log, provide a tamper-proof mechanism to answer the
following important questions:

* What report was being analyzed?
* Who did the analysis?
* What policy was applied to the report?
* Who authored individual policy rules applied to the report?
* When where those rules defined?
* What time was the report analyzed?

Check out [Red Light Green Light](https://github.com/atgreen/red-light-green-light) at https://github.com/atgreen/red-light-green-light !

- Anthony
