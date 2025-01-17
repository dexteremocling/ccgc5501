Challenge 1 entitled “Buckets of Fun” leaves a clue stated as “We all know that public buckets are risky. But can you find the flag?”. 
It involves an S3 bucket with an IAM policy that allows the objects to be accessed externally by the public. 
To find the flag, I’ve taken the following steps:
a.   The clue indicated that the flag is stored in an S3 bucket. 
     Looking at the given IAM Policy, the directory for the S3 bucket is presented as Resource and s3:prefix.
     
     The path is s3://thebigiamchallenge-storage-9979f4b/files/

b.  To list the contents of the bucket, I’ve looked below references online to obtain the AWS CLI command syntax:
       -   https://blog.awsfundamentals.com/aws-s3-ls by Tobias Schmidt dated Aug 15, 2023
       -   https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html

    The command is: 
       aws s3 ls s3://thebigiamchallenge-storage-9979f4b/files/

c.  Using the above command, I was able to see two files:
       -   flag1.txt
       -   logo.png
d.  To open the flag1.txt, I used the command: 
       aws s3 cp s3://thebigiamchallenge-storage-9979f4b/files/flag1.txt –
e.  The content of flag1.txt shows:
       {wiz:exposed-storage-risky-as-usual}

