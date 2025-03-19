# Challenge: CloudFoxable It's a secret

## Challenge Statement:

Assumed Breach: Principal

---

## Challenge Details:

For this CTF, your starting CTF user has the following policies:
•	SecurityAudit (AWS Managed)
•	CloudFox (Customer Managed)
•	its-a-secret (Customer Managed)

The first two policies allow you to run CloudFox. The third policy allows this starting user to get the flag for this challenge. If you followed the setup steps in the First Flag challenge (if you are doing this in a workshop, the setup in First Flag has been done for you), you'll have a profile called cloudfoxable which is tied to the user/ctf-starting-user.

To confirm this, run aws --profile cloudfoxable sts get-caller-identity.

Now run cloudfox using the cloudfoxable profile and see if you can access the secret named its-a-secret.

---

## Solution:

To find the flag, refer to the following procedure:

a.	In AWS CLI, set the cloudfoxable profile which is associated to IAM user ctf-starting-user.
```
set AWS_PROFILE=cloudfoxable
```

b.	Verify it using below command.
```
aws sts get-caller-identity
```

c.	Check the IAM permission policies attached to the ctf-starting-user. The command and corresponding output are shown below.

Command:
```
aws iam list-attached-user-policies --user-name ctf-starting-user --profile cloudfoxable
```

Output:
```
{
    "AttachedPolicies": [
        {
            "PolicyName": "CloudFox-policy-perms",
            "PolicyArn": "arn:aws:iam::202533511029:policy/CloudFox-policy-perms"
        },
        {
            "PolicyName": "its-a-secret-policy",
            "PolicyArn": "arn:aws:iam::202533511029:policy/its-a-secret-policy"
        },
        {
            "PolicyName": "SecurityAudit",
            "PolicyArn": "arn:aws:iam::aws:policy/SecurityAudit"
        }
    ]
}
```

d.	From the list, the its-a-secret-policy looks interesting obviously so I look further into it. The command and corresponding output are shown below.

Command:
```
aws iam get-policy --policy-arn arn:aws:iam::202533511029:policy/its-a-secret-policy --profile cloudfoxable
```

Output:
```
{
    "Policy": {
        "PolicyName": "its-a-secret-policy",
        "PolicyId": "ANPAS6J7QD52XM25IPUJI",
        "Arn": "arn:aws:iam::202533511029:policy/its-a-secret-policy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 1,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "Description": "policy that only allows access to the its-a-secret flag",
        "CreateDate": "2025-03-18T14:53:55+00:00",
        "UpdateDate": "2025-03-18T14:53:55+00:00",
        "Tags": []
    }
}
```

Command:
```
aws iam get-policy-version --policy-arn arn:aws:iam::202533511029:policy/its-a-secret-policy --version-id v1 --profile cloudfoxable
```
 
Output:
```
{
    "PolicyVersion": {
        "Document": {
            "Statement": [
                {
                    "Action": [
                        "ssm:GetParameter"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-a-secret"
                    ]
                }
            ],
            "Version": "2012-10-17"
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2025-03-18T14:53:55+00:00"
    }
}
```

e.	It shows that the policy allows ssm:GetParameter on the resource “arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-a-secret”

f.	Open the resource. It appears that the resource is AWS Systems Manager Parameter Store.
As define in AWS website, it provides secure, hierarchical storage for configuration data management and secrets management.
		Command:
```
aws ssm get-parameter --name /cloudfoxable/flag/its-a-secret  --profile cloudfoxable
```
 
Output:
```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-a-secret",
        "Type": "SecureString",
        "Value": "AQICAHgpNzUzFOSjtzdhTAkdjoyA+OjVh0q8W9UfuG8D5JnNcQHRYMyULVBDWxBsaybf1wXEAAAAojCBnwYJKoZIhvcNAQcGoIGRMIGOAgEAMIGIBgkqhkiG9w0BBwEwHgYJYIZIAWUDBAEuMBEEDC/ylUnX/uqf9HGleAIBEIBb0/hLSLpOHkku7DeBR6q9FdHaMAgwCy+YJX1yhMglHVmnWNsVB/xMwNqqI+oH3EZXnTzVP2mBCiBQZqw0W5cKG3TB162srhkX1tVX9603ABQGvCjjhnWAD5ep/w==",
        "Version": 1,
        "LastModifiedDate": "2025-03-18T10:53:51.819000-04:00",
        "ARN": "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-a-secret",
        "DataType": "text"
    }
}
```

g.	I did not realize at first but it shows that the value is SecureString type (encrypted). Run below command to decrypt it. The output shows the flag.

Command:
```
aws ssm get-parameter --name /cloudfoxable/flag/its-a-secret  --with-decryption --profile cloudfoxable
```

Output:
```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-a-secret",
        "Type": "SecureString",
        "Value": "FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}",
        "Version": 1,
        "LastModifiedDate": "2025-03-18T10:53:51.819000-04:00",
        "ARN": "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-a-secret",
        "DataType": "text"
    }
}
```

h.	The flag is: 
FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}

---

## Reflection:

### What was your approach?
As my cloudfoxable profile indicates InvalidClientTokenId error, my approach started with setting up again the profile in AWS CLI, which took some time as I had to figure out how to configure the credentials properly. After that, I did not use CloudFox, as initially suggested in the procedure. Instead, I focused on investigating the IAM policies attached to the ctf-starting-user associated to the cloudfoxable profile. I reviewed these policies to understand what permissions were granted, especially looking at the its-a-secret policy. I then tried to access the secret using the ssm:GetParameter command.

### What was the biggest challenge?
The biggest challenge was realizing that the policy provided access to an SSM parameter instead of a Secrets Manager secret, and I had to decrypt the value of the SecureString stored in that parameter. Initially, I was trying to access the secret in the Secrets Manager, and I didn’t initially realize that the value needed to be decrypted since it was stored as SecureString.

### How did you overcome the challenges?
I overcame these challenges by reviewing the policy again and noticing that it was granting access to an SSM parameter using ssm:GetParameter and not secretsmanager:GetSecretValue. After realizing this, I switched to using the ssm command. However, I faced another challenge as the parameter value was encrypted as SecureString. I had to use the --with-decryption flag in the ssm get-parameter command to retrieve the decrypted value of the parameter, which revealed the flag.

### What led to the breakthrough?
The breakthrough came when I understood that the secret was an SSM parameter, and then that it was encrypted as a SecureString, I used the proper command and flags (--with-decryption) to retrieve the flag.

### On the blue side, how can the learning be used to properly defend the important assets?
This experience teaches the importance of thoroughly understanding IAM policies to ensure that only the intended resources and actions are accessible. Also, it emphasizes the use of encryption to protect sensitive data. Using the --with-decryption flag only when necessary can help protect sensitive data and prevent unauthorized access. 
