# cloudlaunch
AWS assessment project for secure static website hosting and VPC design.

## Task 1: Static Website Hosting on S3 with IAM

### Description
I successfully deployed a static website for CloudLaunch using Amazon S3 and configured fine-grained access controls using IAM policies. I created three S3 buckets with distinct purposes and a dedicated IAM user with permissions scoped precisely to the assignment requirements.

### S3 Buckets Created
1.  **`cloudlaunch-site-bucket-abc`**: Hosts the public-facing website. Static website hosting was enabled, and a bucket policy allows public read access to objects.
2.  **`cloudlaunch-private-bucket-abc`**: A private bucket for internal documents. Access is strictly limited to the `cloudlaunch-user` who has `GetObject` and `PutObject` permissions.
3.  **`cloudlaunch-visible-only-bucket-abc`**: A private bucket where the `cloudlaunch-user` can only see the bucket name in a list (`ListBucket`) but cannot access any objects inside.

### IAM User and Policy
- **IAM User:** `cloudlaunch-user`
- **Policy:** A custom IAM policy named `CloudLaunchS3Policy` was created and attached to the user. The policy grants:
  - `ListBucket` on all three buckets.
  - `GetObject` on the site bucket (read website files).
  - `GetObject` and `PutObject` on the private bucket.
  - No `DeleteObject` permissions anywhere.
  - No access to the contents of the visible-only bucket.

### URLs
- **S3 Website Endpoint:** http://cloudlaunch-site-bucket-oyinda.s3-website.eu-north-1.amazonaws.com


### IAM User Credentials & Console Login
**Account ID:** 1490-6957-9844
**Account Alias:** `cloudlaunch-assignment` (Optional: you can create one in IAM Settings)
**IAM User Name:** `cloudlaunch-user`
**Console Login URL:** https://123456789012.signin.aws.amazon.com/console
*(Note: The user was created for programmatic access only. To enable console login, you would need to add a password and the required permissions to view the VPC.)*

**Attached IAM Policy (JSON):{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListAllBuckets",
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "*"
        },
        {
            "Sid": "ListSiteBucket",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::cloudlaunch-site-bucket-oyinda"
        },
        {
            "Sid": "GetSiteBucketObjects",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch-site-bucket-oyinda/*"
        },
        {
            "Sid": "ListPrivateBucket",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::cloudlaunch-private-bucket-oyinda"
        },
        {
            "Sid": "ReadWritePrivateBucketObjects",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::cloudlaunch-private-bucket-oyinda/*"
        },
        {
            "Sid": "ListVisibleOnlyBucket",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::cloudlaunch-visible-only-bucket-oyinda"
        }
    ]
}
