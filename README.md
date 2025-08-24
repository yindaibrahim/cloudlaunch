# CloudLaunch AWS Deployment

**AWS assessment project for secure static website hosting and VPC design.**

## Task 1: Static Website Hosting on S3 with IAM

I set up a static website using Amazon S3 and created a secure system for managing file access with IAM. This involved creating three different buckets, each with a specific purpose and security level.

### S3 Buckets & Their Roles
1.  **`cloudlaunch-site-bucket-oyinda`** (Public): Hosts the company website. Enabled static website hosting and added a bucket policy for public read access.
2.  **`cloudlaunch-private-bucket-oyinda`** (Private): Stores internal documents. Only the `cloudlaunch-user` can read or upload files here.
3.  **`cloudlaunch-visible-only-bucket-oyinda`** (Restricted): The IAM user can see this bucket exists but cannot access any of its contents.

### IAM User & Permissions
- **User:** `cloudlaunch-user`
- **Policy:** `CloudLaunchS3Policy` (Custom JSON policy)
- **Permissions:**
  - Can **list** all three buckets.
  - Can **read** files from the public website bucket.
  - Can **read** and **upload** files to the private bucket.
  - **Cannot** delete anything.
  - **Cannot** read files from the visible-only bucket.

### Website URL
- **S3 Website URL:** http://cloudlaunch-site-bucket-oyinda.s3-website.eu-north-1.amazonaws.com

### Access Details & Policy
**Account ID:** `149069579844`
**IAM User Name:** `cloudlaunch-user`

**The full IAM policy I created and attached:**
```json
{
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
```
The user access keys: AKIASFNJ5IZCHK6LSDU2
Secret key: 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
---

## Task 2: VPC Network Design

### What I Built
I designed a secure VPC network to prepare for future application and database servers, following best practices for isolation and security.

### VPC Details
- **VPC Name:** `cloudlaunch-vpc`
- **CIDR Block:** `10.0.0.0/16`

### Subnets
| Subnet Name               | CIDR Block      | Type    | Purpose |
| ------------------------- | --------------- | ------- | ------- |
| `cloudlaunch-public-subnet`  | `10.0.1.0/24`   | Public  | For load balancers / public services |
| `cloudlaunch-app-subnet`     | `10.0.2.0/24`   | Private | For application servers (EC2) |
| `cloudlaunch-db-subnet`      | `10.0.3.0/28`   | Private | For databases (RDS) |

### Key Components
- **Internet Gateway:** `cloudlaunch-igw` (Attached to the VPC)
- **Route Tables:** Separate tables for public and private subnets. Only the public route table has a route to the internet.
- **Security Groups:**
  - **`cloudlaunch-app-sg`:** Allows HTTP (port 80) traffic only from within the VPC.
  - **`cloudlaunch-db-sg`:** Allows MySQL (port 3306) traffic only from the application subnet (`10.0.2.0/24`).
