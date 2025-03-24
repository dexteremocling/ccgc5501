# Challenge: CloudFoxable It's Another Secret

## Challenge Statement:
Assumed Breach: Principal

---

## Challenge Details:

TL;DR: You've just gained access to the role Ertz. Can you find and access the its-another-secret flag?

A lot of the challenges in the category Assumed Breach: Principal will have you assume into a role to simulate a new starting point. You'll technically start as ctf-starting-user, but your first action will be to assume the role Ertz listed above. This is to simulate a scenario where you've just gained access to the role Ertz.

You can do this the "hard way" by running aws --profile cloudfoxable sts assume-role --role-arn arn:aws:iam::ACCOUNT_ID:role/Ertz --role-session-name Ertz and then using that output to set up a new profile or environment variables.

Or, you can do it the easy way and create a new profile that does the role assumption for you!

Edit ~/.aws/config and add the following profile:

[profile ertz]
region = us-west-2
role_arn = arn:aws:iam::ACCOUNT_ID:role/Ertz
source_profile = cloudfoxable

Now verify it!

❯ aws --profile ertz sts get-caller-identity
{
    "UserId": "AROAQXHJKLZKFYSRACOES:botocore-session-1684201365",
    "Account": "ACCOUNT_ID",
    "Arn": "arn:aws:sts::ACCOUNT_ID:assumed-role/Ertz/botocore-session-1684201365"
}

For each of these challenges, and on a cloud penetration test, the next thing you'll want to do is see what permissions the assumed breach user has, and see if any of them are "interesting".

If you've completed It's a secret, the rest of this challenge will look the same. The main goal here is to make sure you are comfortable assuming new roles. Good luck!

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

c.	As per instruction, verify that ctf-starting-user can assume the Ertz role.

Command:
```
aws iam get-role --role-name ertz --profile cloudfoxable
```

Output:
```
{
    "Role": {
        "Path": "/",
        "RoleName": "Ertz",
        "RoleId": "AROAS6J7QD527XRQMYDB3",
        "Arn": "arn:aws:iam::202533511029:role/Ertz",
        "CreateDate": "2025-03-24T14:57:39+00:00",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "AWS": "arn:aws:iam::202533511029:user/ctf-starting-user"
                    },
                    "Action": "sts:AssumeRole"
                }
            ]
        },
        "MaxSessionDuration": 3600,
        "RoleLastUsed": {}
}
```

d.	Assume the Ertz role. The command and corresponding output are shown below.

Command:
```
aws sts assume-role --role-arn arn:aws:iam::202533511029:role/Ertz --role-session-name Ertz --profile cloudfoxable
```

Output:
```
{
    "Credentials": {
        "AccessKeyId": "ASIAS6J7QD526K6SRVPY",
        "SecretAccessKey": "OwAOxaT1hc3bKHcyRzfc0teeDXKc0XkulwSCD+5w",
        "SessionToken": "IQoJb3JpZ2luX2VjEJf//////////wEaCXVzLXdlc3QtMiJHMEUCIQCaGrX8u0j7XSO8JcBaYOUfBrgnuvUdGUHSYlyc8FliRAIgQX6zHFtbIKctele7Di0OPsewbeYtkqYYjvXqHo86bSkqmgII8P//////////ARAAGgwyMDI1MzM1MTEwMjkiDMFPWmzXeWQr4E2fwSruAZpFNW9BQoj2HZE36pnoq6jRJmZxdR17YjzZB28+opZgo2d3LjFPAdI5wiWRfq1VC2PfpyQeAj1LPAuscZORcvo1p6EYKLiH3/Jd4bK6bogziOi6nexeL8X3/3ua2x46Jr/CGq+NJOlKG1By3kCC1o83n57dyPSuo4FXxrjRtXKYeE17NblRFng9bvtH/QGDSdTAUeY83UpwyzZoZ8R7M/B0tN5YBLqOW6g9Ol1cyHbHycD3RXQ/nEKzGAay1ZtytDowzy02mFs3c1KzwKQVKUGyVNzT1vTRm524t5PHaSBSHeL2tw8rbICNCNDl7QIw3+uFvwY6nQGsLfbAyJFLbVzZn6LQgaJlkkAvOlqJuiFAaGrGa0j3TtyPIYM0q5N1TJPfCT1ifW3dcOYyA4Tq7ZZluw57R05qpm8lF2RJTr3FcMYVGEzALh7YRu+MvjX42h24Rhqh5KlhW/Ju5mv/2H9ybrkO40fh1coWGePR85iwOg9w2L2hOHms9WPtncZfHcizowd2zZcKw3wdQUcalzzioasg",
        "Expiration": "2025-03-24T16:10:23+00:00"
    },
    "AssumedRoleUser": {
        "AssumedRoleId": "AROAS6J7QD527XRQMYDB3:Ertz",
        "Arn": "arn:aws:sts::202533511029:assumed-role/Ertz/Ertz"
    }
}
```

e.	Using the output above, set up a new profile. Edit ~/.aws/config and add the following profile:
```
[profile ertz]
region = us-west-2
role_arn = arn:aws:iam::202533511029:role/Ertz
source_profile = cloudfoxable
```

f.	Verify it.
Command:
```
aws --profile ertz sts get-caller-identity
```
 
Output:
```
{
    "UserId": "AROAS6J7QD527XRQMYDB3:botocore-session-1742829648",
    "Account": "202533511029",
    "Arn": "arn:aws:sts::202533511029:assumed-role/Ertz/botocore-session-1742829648"
}
```

g.	Check the permissions of Ertz role.
Command:
```
aws iam list-attached-role-policies --role-name ertz --profile cloudfoxable
```
 
Output:
```
{
    "AttachedPolicies": [
        {
            "PolicyName": "its-another-secret-policy",
            "PolicyArn": "arn:aws:iam::202533511029:policy/its-another-secret-policy"
        }
    ]
}
```

Command:
```
aws iam get-policy-version --policy-arn arn:aws:iam::202533511029:policy/its-another-secret-policy --version-id v1 --profile cloudfoxable
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
                        "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-another-secret"
                    ]
                }
            ],
            "Version": "2012-10-17"
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2025-03-24T14:57:34+00:00"
    }
}
```

h.	The same as the previous lab “It’s a secret”, the policy allows to perform ssm:GetParameter on resource arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-another-secret

i.	Open the resource. It appears that the resource is AWS Systems Manager Parameter Store.
As define in AWS website, it provides secure, hierarchical storage for configuration data management and secrets management.
Command:
```
aws ssm get-parameter --name /cloudfoxable/flag/its-another-secret --profile ertz
```
 
Output:
```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-another-secret",
        "Type": "SecureString",
        "Value": "AQICAHgpNzUzFOSjtzdhTAkdjoyA+OjVh0q8W9UfuG8D5JnNcQHfSYVFKqC1Yq4luFgwFujiAAAAoTCBngYJKoZIhvcNAQcGoIGQMIGNAgEAMIGHBgkqhkiG9w0BBwEwHgYJYIZIAWUDBAEuMBEEDCN9zo349fzytewxCAIBEIBamrnL4zocleJpPqR5oiXywxnMlZ8iCv5Q+zmdUekb4EW7CuIlnr+ovHFzEEWfPGdGYgrlajFV5MxGOWYxOR5ToBeKF0aES8x480juiB1pFv5sr0TRYhdlnIQV",
        "Version": 1,
        "LastModifiedDate": "2025-03-24T10:57:29.099000-04:00",
        "ARN": "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-another-secret",
        "DataType": "text"
    }
}
```

j.	The output above shows that the value is SecureString type (encrypted). Run below command to decrypt it. The output shows the flag.

Command:
```
aws ssm get-parameter --name /cloudfoxable/flag/its-another-secret --with-decryption --profile ertz
```

Output:
```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-another-secret",
        "Type": "SecureString",
        "Value": "FLAG{ItsAnotherSecret::ThereWillBeALotOfAssumingRolesInThisCTF}",
        "Version": 1,
        "LastModifiedDate": "2025-03-24T10:57:29.099000-04:00",
        "ARN": "arn:aws:ssm:us-west-2:202533511029:parameter/cloudfoxable/flag/its-another-secret",
        "DataType": "text"
    }
}
```

k.	The flag is: 
FLAG{ItsAnotherSecret::ThereWillBeALotOfAssumingRolesInThisCTF}

---

## Reflection:

### What was your approach?
This lab is similar to the previous one so I’ve just followed as per the specified instructions. First, I’ve assumed the ertz role using AWS CLI. I started by listing the attached policies for the role to see what permissions were available. After identifying its-another-secret-policy, I retrieved its details to understand what actions I could perform. Then, I checked AWS Systems Manager (SSM) to see if any stored parameters contained the flag. Finally, I used the --with-decryption option to retrieve the flag.

### What was the biggest challenge?
The biggest challenge was understanding why the profile setup required editing ~/.aws/config instead of ~/.aws/credentials. In previous exercises, I had only modified ~/.aws/credentials, so this was unexpected but a great way to learn.

### How did you overcome the challenges?
I compared the use cases of ~/.aws/config and ~/.aws/credentials. I learned that ~/.aws/config is used for setting role assumption parameters, while ~/.aws/credentials is primarily for storing static access keys. This clarified why I needed to modify the config file for role switching.

### What led to the breakthrough?
The breakthrough happened when I retrieved the flag from SSM using --with-decryption. At first, the flag appeared encrypted, so I needed to include that option. Understanding the permissions granted by its-another-secret-policy also guided me.

### On the blue side, how can the learning be used to properly defend the important assets?
This exercise highlights the importance of monitoring and restricting IAM roles and policies. Organizations should implement least privilege access, and regularly review policies for unnecessary permissions. Additionally, sensitive data stored in AWS SSM should have strict access controls and be encrypted to prevent unauthorized access.
