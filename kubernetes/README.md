# Cronjob for snapshotting OpenBao running on Kubernetes

This assumes the Kubernetes/JWT authentication backend is configured in OpenBao.

The script being executed in this cronjob, is authenticating with OpenBao using the Kubernetes authentication backend, via its serviceaccount JWT.

The role and policy being used must be created before hand and must be used by the cronjob.

After the snapshot is created in a temporary directory, `s3cmd` is used to sync it to a s3 endpoint.

## Configuration over environment variables

* `BAO_ADDR`  - OpenBao address to access
* `BAO_ROLE` - OpenBao role to use to create the snapshot
* `BAO_AUTH_PATH` - The path of the Kubernetes authentication backend in Vault (e.g. `kubernetes`)
* `S3_URI` - S3 URI to use to upload (s3://xxx)
* `S3_BUCKET` - S3 bucket to point to
* `S3_HOST` - S3 endpoint
* `S3_EXPIRE_DAYS` - Delete files older than this threshold (expired)
* `S3_TLS_ARGS` - To specify an explicit CA certificate to use or skip the TLS verification
* `AWS_ACCESS_KEY_ID` - Access key to use to access S3
* `AWS_SECRET_ACCESS_KEY` - Secret access key to use to access S3

## Configuration of file retention (pruning)

With AWS S3, use [lifecycle
rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-expire-general-considerations.html)
to configure retention and automatic cleanup action (prune) for expired files.

For other S3 compatible storage, ensure to set Governance
lock/Object lock to avoid any modification before `$S3_EXPIRE_DAYS`.
