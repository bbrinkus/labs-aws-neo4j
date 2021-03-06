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

## Cloud-init

Example init script that downloads the latest database version and set the initial password for the neo4j user.
The access key has a restricted readonly S3 role to the database bucket.

```
#!/bin/bash

echo "Start cloud init"

NEO_PASSWORD="<neo4j-new-password>"
export AWS_ACCESS_KEY_ID="<aws-access-key>"
export AWS_SECRET_ACCESS_KEY="<aws-secret-key>"

echo "Update system packages"
yum update -y

echo "Initialize new password to the neo4j user"
neo4j-admin set-initial-password ${NEO_PASSWORD}
chown -R neo4j:neo4j /var/lib/neo4j/data/dbms/

echo "Update Neo4j database"
/opt/labs/neo4j-get-database -b <s3-bucket-name>

echo "Finish cloud init"
```

## IAM Policy

This policy will restrict the list and get access to the database bucket only.

```
#
# CloudFormation configuration
#
DatabaseS3ROPolicy:
  Type: "AWS::IAM::Policy"
  Properties:
    PolicyName: "DatabaseS3ReadOnly"
    PolicyDocument:
      Version: "2012-10-17"
      Statement:
        -
          Effect: "Allow"
          Action:
            - "s3:Get*"
            - "s3:List*"
          Resource:
            - "arn:aws:s3:::<s3-bucket-name>"
            - "arn:aws:s3:::s3-bucket-name/*"
    Groups:
      -
        Ref: "DatabaseS3ROGroup"

DatabaseS3ROGroup:
  Type: AWS::IAM::Group
  Properties:
    Path: "/"
```

## Packer notes

### Directory upload

If the source is /foo (no trailing slash), and the destination is /tmp, then the contents of /foo on the local machine will be uploaded to /tmp/foo on the remote machine. The foo directory on the remote machine will be created by Packer.
If the source, however, is /foo/ (a trailing slash is present), and the destination is /tmp, then the contents of /foo will be uploaded into /tmp directly.
