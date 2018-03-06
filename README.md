# AWS Neo4j AMI

Create a new AMI based on the latest Amazon EC2 Linux AMI that contains the latest Neo4j community edition.

## Packer Notes

### Directory upload

If the source is /foo (no trailing slash), and the destination is /tmp, then the contents of /foo on the local machine will be uploaded to /tmp/foo on the remote machine. The foo directory on the remote machine will be created by Packer.
If the source, however, is /foo/ (a trailing slash is present), and the destination is /tmp, then the contents of /foo will be uploaded into /tmp directly.
