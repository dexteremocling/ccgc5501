# Challenge Number: 1

## Challenge Statement:

Buckets of Fun – We all know that public buckets are risky. But can you find the flag?

---

## IAM Policy:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                }
            }
        }
    ]
}
```

---

## What do I have access to?

- I can read objects (files) stored in the S3 bucket with directory “thebigiamchallenge-storage-9979f4b” as the access is allowed within the bucket.
- I can list the contents of the S3 bucket for objects that have the prefix “files/”.

---

## What don't I have access to?

- I cannot upload, modify, or delete any objects in the bucket as it is not included in the policy.

---

## What do I find interesting?

- It allows access to everyone which is risky as it makes the bucket accessible to the public. This could lead to unintended exposure of sensitive data.

---

## Solution:

To find the flag, I’ve taken the following steps:

a. The clue indicated that the flag is stored in an S3 bucket. Looking at the given IAM Policy, the directory for the S3 bucket is presented as Resource and s3:prefix.

   The path is s3://thebigiamchallenge-storage-9979f4b/files/

b. To list the contents of the bucket, I’ve looked below references online to obtain the AWS CLI command syntax:
- https://blog.awsfundamentals.com/aws-s3-ls by Tobias Schmidt dated Aug 15, 2023
- https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html

   The command is: 
   ```
   aws s3 ls s3://thebigiamchallenge-storage-9979f4b/files/
   ```

c. Using the above command, I was able to see two files:
- flag1.txt
- logo.png

d. To open the flag1.txt, I used the command: 
   ```
   aws s3 cp s3://thebigiamchallenge-storage-9979f4b/files/flag1.txt –
   ```

e. The content of flag1.txt shows:
   ```
   {wiz:exposed-storage-risky-as-usual}
   ```

---

## Reflection:

### What was your approach?

The clue indicated that the flag is stored in an S3 bucket. Looking at the IAM Policy, I found the directory path for the S3 bucket. I researched AWS CLI command syntax from online guides on how to list and open files in an S3 bucket.

### What was the biggest challenge?

The hardest part was figuring out the correct AWS CLI commands and understanding the IAM policy.

### How did you overcome the challenges?

I studied the policy carefully, looked up command syntax online, and tested each command step-by-step.

### What led to the breakthrough?

The breakthrough came from understanding the IAM Policy, particularly the Action and Resource fields which indicated the access and directory.

### On the blue side, how can the learning be used to properly defend the important assets?

To defend important assets:
a. Restrict public access  
b. Use conditions like IP restrictions, time-based access, or multi-factor authentication  
c. Grant users only the permissions they need and nothing more
