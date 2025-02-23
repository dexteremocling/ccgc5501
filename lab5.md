# Challenge: AWS Environment Setup

---

## Setup Instructions:

1.	Select or create an AWS account. (Do NOT use an account that has any production resources or sensitive data!)
2.	Create a non-root user with administrative access that you will use when running Terraform.
3.	Create an access key for that user.
4.	Install the AWS CLI.
5.	Configure your AWS CLI with your newly created admin user as the default profile.
6.	Confirm your CLI is working as expected by executing aws sts get-caller-identity.
7.	Install the Terraform binary and add the binary location to your path.
8.	Set up AWS CLI with the ctf-starting-user Profile as per Terraform output.

---

## Solution:

To setup the AWS environment, refer to the following procedure:

a.	Open web browser and go to https://aws.amazon.com/. Click "Create an AWS Account". Enter Account Details – Root user email address, AWS account name. Click Verify email address, then proceed for verification.

b.	Create a Root User Password. Enter Contact Information: Select "Personal" (for individual use). Fill in full name, phone number, country, and address. Click "Continue". Enter Payment Information: Use the Prepaid Credit Card purchased previously. AWS Free Tier is available. Verify identity.

c.	Sign In to the AWS Account. Log in using the root email and password.

d.	Now, create an IAM user with Admin privileges. In the AWS Console, search for IAM in the search bar. Click on IAM to open the Identity and Access Management dashboard. In the IAM dashboard, click on Users in the left-hand menu and Add users button. Enter non-root-admin-user as the User name. Under Select AWS access type, check "Access key - Programmatic access" essential for AWS CLI. Click Next: Permissions and Select "Attach policies directly". In the search bar, type AdministratorAccess and check the box next to AdministratorAccess. Click Next: Review to confirm your settings. Click Create user.

e.	Once the user is created, AWS will show the Access Key ID and Secret Access Key. Save the credentials securely (e.g., download the .csv file).

f.	Download the AWS CLI Installer on this link: https://aws.amazon.com/cli/. Open the downloaded file and follow the setup wizard. To confirm that AWS CLI is installed correctly, open Command Prompt and run the following command:

```
aws --version
```

g.	After installation, configure AWS CLI with the credentials. Run the following command. It will prompt to enter AWS Access Key ID, AWS Secret Access Key, Default region, and Default output format. Take the details from the downloaded .csv file from Step e above.

```
aws configure
```

h.	Verify AWS CLI is working. Run the following command to check the identity:

```
aws sts get-caller-identity
```

i.	Download Terraform from the official site https://developer.hashicorp.com/terraform/install. Choose the AMD64 for 64-bit version of Windows. Extract the terraform.exe file to preferred directory. Add the Terraform directory to the System PATH (Environment Variables).

j.	Check if Terraform is recognized using the following command:

```
terraform -version
```

k.	As per Terraform output, set up AWS CLI with the cloudfoxable Profile. The Terraform output provides access keys for configuring the Profile. Run the aws configure command to configure. When prompted, input the Access Key ID and Secret Access Key from the Terraform output. To make sure the ctf-starting-user profile was set up correctly, run:

```
aws sts get-caller-identity --profile cloudfoxable
```

---

## Reflection:


### What are some "security" measures you have implemented and why?
To enhance security, I have created an admin user instead of using the root account which reduce the risk of accidental misconfigurations or security breaches. I have generated AWS Access Key ID & Secret Access Key for the new admin user allowing secure access to AWS without logging in with the root account. To prevent exposure, the credentials were stored in the AWS credentials file rather than sharing publicly.

### Why do we even need an "admin" user when you have access to the root user?
The root user has unrestricted control over AWS, making it too powerful and risky to use regularly. Creating the admin user adds an extra layer of security and control ensuring actions can be tracked and minimizing the risk of compromising the entire AWS account.

### What was your approach?
The instructions were detailed so I just followed as specified. Although, several issues and errors were encountered along the way that need to be resolved in order to move forward.

### What was the biggest challenge?
The biggest challenge was setting up the AWS credentials correctly. It was tricky to generate the access keys, and configure the AWS CLI without running into errors. I had to double-check the credentials file, fix profile settings, and troubleshoot issues like invalid tokens, which made the process more time-consuming than expected.

### How did you overcome the challenges?
I overcame the challenge by manually setting up the credentials in the C:\Users\Dex\.aws directory. I edited the credentials file myself, carefully adding the Access Key ID and Secret Access Key under the correct profile.
