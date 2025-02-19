# Challenge: CloudFoxable Challenge 1

## Challenge Statement:
Do this first!
First Flag
---

## Setup Instructions:
Note that AWS Environment Setup is part of Lab 5 hence it is excluded in this write up.
The following setup instruction will be performed via AWS CLI.

1.	git clone https://github.com/BishopFox/cloudfoxable
2.	cd cloudfoxable/aws
3.	cp terraform.tfvars.example terraform.tfvars
4.	terraform init
5.	Edit terraform.tfvars and set the AWS profile you'd like to use: aws_local_profile = "YOUR_PROFILE"
6.	(Optional) terraform plan
7.	terraform apply
8.	Find the Flag. Read the stuff at the end of the terraform apply output closely enough, and you'll find the first flag. The Terraform output will show you how to set up your AWS CLI with the ctf-starting-user.
By the way, the flag syntax is always as shown below. You will always submit this whole string as shown above with the word flag.

FLAG{challengeName::CamelCaseText}

---

## Solution:

To find the flag, refer to the following steps:

a.	The instructions were detailed so I just followed as specified.

b.	In AWS CLI, run the following commands:
```
git clone https://github.com/BishopFox/cloudfoxable
cd cloudfoxable/aws
```

c.	The cp command is not recognized in command prompt. The updated command is: 
```
copy terraform.tfvars.example terraform.tfvars
```

d.	Navigate to the directory where the terraform.tfvars file is located. It is located in “C:\Users\<Your-Username>\cloudfoxable\aws”. Using Notepad for Windows, open terraform.tfvars to set your AWS Profile.

e.	Once the terraform.tfvars file is open, you'll need to locate the line where the AWS profile is defined and replace "YOUR_PROFILE" with the name of the AWS CLI profile you want to use. Save the file.
I’ve used my non-root-admin-user profile.
```
aws_local_profile = "non-root-admin-user"
```

f.	To verify or check your current AWS CLI profile on Windows, run below command. This will show the current configuration of your AWS CLI, including the profile being used.
```
aws configure list
```

g.	To see all profiles available to your AWS CLI, check the AWS credentials file. It is located in “C:\Users\<Your-Username>\.aws\credentials”. This can be opened using Notepad for Windows. You should see an entry for the non-root-admin-user profile like this:
```
[non-root-admin-user]
aws_access_key_id = YOUR_ACCESS_KEY_ID
aws_secret_access_key = YOUR_SECRET_ACCESS_KEY
region = us-east-1
```

h.	If you want to set a specific profile to be used by your commands, use below command. Then you can check again using “aws configure list” to ensure the profile has been switched.
```
set AWS_PROFILE=non-root-admin-user
```

i.	Run the commands:
```
terraform init
terraform plan
terraform apply
```

j.	The output of terraform apply includes the flag. It shows as follows:
```
You'll need this: FLAG{congrats_you_are_now_a_terraform_expert_happy_hunting}
```
---

## Reflection:

### What was your approach?
The instructions were detailed so I just followed as specified. Although, several issues and errors were encountered along the way that need to be resolved in order to move forward.

### What was the biggest challenge?
The biggest challenge was troubleshooting AWS CLI authentication errors, especially with invalid credentials and profile misconfigurations.

### How did you overcome the challenges?
I overcame these challenges by carefully reviewing error messages and researching solutions online.

### What led to the breakthrough?
The breakthrough moment was once I got the profile set up properly. I could successfully authenticate and interact with AWS resources.

### On the blue side, how can the learning be used to properly defend the important assets?
This exercise showed me how misconfigured AWS permissions can expose sensitive data.
