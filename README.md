# sample-popeye-policy

This is sample [Red Light Green Light](https://rl.gl) (rlgl) policy
for analyzing [Popeye](https://popeyecli.io/) k8s sanitization
reports.  Red Light Green Light makes it easy to build automation
based on the application of custom defined policy against reports
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
RED: https://rl.gl/doc?id=RLGL-BK6AQACL (sha3/256: 8e78e4ea403d5d24ef6e6d6b817661c18c2a0ce1032d37ee075ea7ac25a36e32)
$ echo $?
1
```

This tells us that the popeye sanitization report doesn't meet the
policy we've defined in our git repo.  The interesting rules are
defined in the
[XFAIL](https://github.com/atgreen/sample-popeye-policy/blob/main/XFAIL)
file. Click on the link to explore the analysis, which includes direct
links to policy commits in github, and a link to the original report.

The exit code of `1` makes it easy to test is a shell script, allowing
you to send out notifications or similar.

The analysis report was signed by both the rlgl server and your
client, and the server's document signature was uploaded to the
transparency log managed by the [sigstore](https://sigstore.dev)
project.

You can verify all of the signatures and the
[rekor](https://github.com/sigstore/rekor) log entry like so:

```shell
$ rlgl verify RLGL-BK6AQACL  | RLGL_CLIENT_PUBKEY=~/.config/rlgl/public_key.pem sh
Checking document signature: Verified OK
Checking client signature  : Verified OK
Searching for sigstore record:
LogID: c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d
Index: 7878
IntegratedTime: 2021-07-23T22:26:45Z
UUID: 74269476b9c11ec5fa65318625adb67f73d8560a7816eed506f2cb351b764eb3
Body: {
  "RekordObj": {
    "data": {
      "hash": {
        "algorithm": "sha256",
        "value": "d52005ba6e2e7f62c033e78c3496960b216e8bcd84234379c89f602faa2a5b0c"
      }
    },
    "signature": {
      "content": "MGQCMHPWJ8yqDzjB/Gcr8blJCDfREfrEfv9LSnJ/CwozNDQiOnVUWnfaB215Q3QQstMU6QIwDuHp4+0pwrenwWpZy0WAldhg/vGpv70x31PO5/1vMmd+z5g4NWIhsgTXAXnO/rN4",
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

Anthony
