# Challenge: CloudFoxable Bastion 100

## Challenge Statement:
Assumed Breach: Application Compromise/Network Access
---

## Setup Instructions:

1.	Edit cloudfoxable/aws/terraform.tfvars
2.	Switch the challenge flag to true: bastion_enabled = true
3.	terraform apply
4.	The easiest way to access an EC2 is to use SSM. Once the challenge is deployed, run cloudfox aws -p cloudfoxable instances -v2 to find the instance ID.
5.	Next, you'll want in to install the Session Manager plugin
6.	Check out the loot file from the instances command to find the command needed to connect to the instance with SSM.
7.	From there, use cloudfox to figure out what IAM permissions you have access to and find the flag.
---

## Solution:

To find the flag, refer to the following procedure:

a.	The instructions were detailed so I just followed as specified. However, there are many issues and errors encountered (ie. profile, AWS region, cloudfox installation, policies, etc.) hence I did various workarounds to find the flag.

b.	Edit the cloudfoxable/aws/terraform.tfvars. Open the Command Prompt and run the following commands. Note that the succeeding commands are based on my username and directory and will differ on other host.
```
notepad cloudfoxable/aws/terraform.tfvars
```

c.	Switch the challenge flag to true: bastion_enabled = true.

d.	Change the directory to C:\Users\Dex\cloudfoxable\aws. Run the command:
```
cd C:\Users\Dex\cloudfoxable\aws>
```

e.	For this exercise, I’ve used the non-root-admin-user profile and set to us-west-2 region. To verify, run the command:
```
aws sts get-caller-identity
```

f.	Run the commands:
```
terraform init
terraform plan
terraform apply --auto-approve
```

g.	Install CloudFox manually. Download the latest Windows .zip file. Extract the .zip file and keep in preferred directory. Add CloudFox to System PATH. Verify that CloudFox is installed:
```
cloudfox --version
```

h.	Once terraform apply is completed, check if the EC2 instance is running and list down the Instance ID, Instance Name, and Role.
```
cloudfox aws -p non-root-admin-user instances -v2
```

i.	Install AWS SSM Session Manager Plugin. Download and install SSM Plugin as per below link.
```
https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html
```

j.	Connect to the EC2 via SSM using the instance ID i-079dff2720b6a2d73:
```
aws ssm start-session --target i-079dff2720b6a2d73 --profile non-root-admin-user
```

k.	Once inside the EC2 session, CloudFox is not working and could not be installed successfully as well even with different versions. Therefore, I did some alternative approaches using AWS CLI and IAM roles/permissions directly. To check and verify the IAM role associated with the EC2 instance:
```
curl http://169.254.169.254/latest/meta-data/iam/info
```

l.	From the output, I can see the name of the IAM role. The role name is reyna. I’ve used the aws iam commands to list permissions for that role or the policies attached to it, but all are denied. I logged in into the AWS console and added a custom policy and permission for the role reyna.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:GetPolicy",
        "iam:ListPolicies",
        "iam:GetPolicyVersion",
        "iam:ListAttachedRolePolicies",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:ListRoles",
        "iam:GetRole"
      ],
      "Resource": "*"
    }
  ]
}
```

m.	Now, run the command to enumerate the permissions associated with the role.
```
aws iam list-attached-role-policies --role-name reyna
```

n.	Look at the bastion-cloudfoxable-s3. From the description, “Allows the bastion to list and download the contents of the S3 bucket with flag”. My account ID is 202533511029.
```
aws iam get-policy --policy-arn arn:aws:iam::202533511029:policy/bastion-cloudfoxable-s3
```

o.	Open the s3 bucket to find out the permissions and details:
```
aws iam get-policy-version --policy-arn arn:aws:iam::202533511029:policy/bastion-cloudfoxable-s3 --version-id v1
```

p.	It allows the EC2 instance to list and fetch the contents of the bucket cloudfoxable-bastion-zcv2a.
```
aws s3 ls s3://cloudfoxable-bastion-zcv2a 
```

q.	The bucket consists of flag.txt. Display the contents of flag.txt directly in the terminal.
```
aws s3 cp s3://cloudfoxable-bastion-zcv2a/flag.txt –
```

r.	I have found the flag: {FLAG:bastion::ifYouHaveAccessToAnEC2YouHaveAccessToItsIamPermissions}

s.	Exit the bastion instance shell. Clean up the resources that was created by Terraform by running the command:
```
terraform destroy
```

---

## Reflection:

### What was your approach?
I’ve followed the instructions however there are many issues and errors encountered along the way that need to be resolved in order to move forward (ie. profile, AWS region, cloudfox installation, policies, etc.). 

### What was the biggest challenge?
The biggest challenge was dealing with insufficient IAM permissions as well as cloudfox was not recognized inside the bastion instance shell. Many AWS CLI commands failed because my assumed role (reyna) did not have permission to perform actions like iam:GetRole or s3:ListBucket.

### How did you overcome the challenges?
When I encountered various issues and permission errors, I investigated alternative ways to get the necessary information. To work around permission issues, I used metadata queries (curl http://169.254.169.254/latest/meta-data/) to gather information about the instance profile. Via the AWS console, I’ve added a custom policy to the instance profile.

### What led to the breakthrough?
The breakthrough came when I got the permissions for my IAM role (reyna) to get further details about the policies attached to it.

### On the blue side, how can the learning be used to properly defend the important assets?
This exercise showed me the lessons regarding Least Privilege Principle and Instance Metadata Security. Only grant the exact permissions needed and restrict access to meta-data to prevent attackers from retrieving sensitive credentials.
