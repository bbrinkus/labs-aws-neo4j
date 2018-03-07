# AWS Neo4j AMI

Create a new AMI based on the latest Amazon EC2 Linux AMI that contains the latest Neo4j community edition.

## S3 database bucket

The generated databases are stored in an S3 bucket with the following format:

```
<s3-bucket-name>
    * <timestamp>
        * graph.db
            * <database content>
    * <timestamp>
        * graph.db
            * <database content>
```

The timestamp format: `20180306-230923Z` (`date -u +"%Y%m%d-%H%M%SZ"`)

## Packer notes

### Directory upload

If the source is /foo (no trailing slash), and the destination is /tmp, then the contents of /foo on the local machine will be uploaded to /tmp/foo on the remote machine. The foo directory on the remote machine will be created by Packer.
If the source, however, is /foo/ (a trailing slash is present), and the destination is /tmp, then the contents of /foo will be uploaded into /tmp directly.
