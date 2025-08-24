# AltSchool Engineering - Cloud Semester Assignment 1: CloudLaunch

**Student Name: Olamiju Iretunde
**AltSchool ID: ALT/SOE/024/5333

## 📋 Project Overview

This project, **CloudLaunch**, demonstrates a practical implementation of core AWS services as outlined in the assignment. The goal was to host a static website securely on S3 and design a logically isolated VPC network.

## 🚀 Task 1: Static Website Hosting & Secure Storage (S3 & IAM)

### Description
I configured three distinct S3 buckets to separate public and private resources, enforcing strict access controls through a custom IAM policy.

**S3 Buckets Configuration:**

1.  **`cloudlaunch-site-bucket`**
    *   **Purpose:** Hosts the public-facing static website for CloudLaunch.
    *   **Configuration:** Static website hosting was enabled. A bucket policy grants public read-only (`s3:GetObject`) access to the objects, allowing anyone to view the website.
    *   **Security:** All other public access settings are blocked, ensuring only the website files are exposed.

2.  **`cloudlaunch-private-bucket`**
    *   **Purpose:** Stores internal, private documents.
    *   **Configuration:** All public access is explicitly blocked at the bucket level. Access is solely controlled via IAM policies.
    *   **Permissions:** A designated IAM user has permission to upload (`PutObject`) and download (`GetObject`) files but cannot delete them or list the bucket's contents.

3.  **`cloudlaunch-visible-only-bucket`**
    *   **Purpose:** Serves as a test case for granular permission control.
    *   **Configuration:** All public access is blocked.
    *   **Permissions:** The IAM user can see this bucket exists when listing all buckets (`ListBucket`) but is explicitly denied all permissions to read, write, or delete any of its contents, as required.

**IAM User and Fine-Grained Policy:**
I created a dedicated IAM user `Tunde-user` with programmatic access credentials (Access Key & Secret Key). Instead of using AWS managed policies, I authored a custom policy that follows the **principle of least privilege**, granting only the exact permissions necessary for the user to perform its intended tasks and nothing more.

### Live Links
*   **S3 Static Website Endpoint:** http://tunde-assessment.s3-website.eu-north-1.amazonaws.com
### IAM Policy (JSON)
The custom policy `CloudLaunchS3AccessPolicy` attached to the `tunde-user` is defined below:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": [
                "arn:aws:s3:::cloudlaunch-site-bucket",
                "arn:aws:s3:::cloudlaunch-private-bucket",
                "arn:aws:s3:::cloudlaunch-visible-only-bucket"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::cloudlaunch-private-bucket/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cloudlaunch-site-bucket/*"
        }
    ]
}
