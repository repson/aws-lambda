
# Creating an EC2 Instance with Lambda in AWS

## Introduction

In this AWS hands-on lab, we will write a Lambda function that will create an EC2 instance. This Lambda function will be written in Python using the Boto3 library. We will also create a custom Lambda execution policy for our IAM role. When we're done, we will be able to log in to the new EC2 instance via SSH.

## Solution

Log in to the environment using the credentials provided on the lab page. Make sure you're in the us-east-1 (N. Virginia) region throughout the lab.

### Create EC2 Key Pair

1. Navigate to EC2.
2. In the navigation pane, under NETWORK & SECURITY, choose Key Pairs.
3. Choose Create Key Pair.
4. Enter a name for the new key pair (e.g., "LambdaEC2keypair") in the Key pair name field of the Create Key Pair dialog box, and then choose Create.
5. Save the private key file in a safe place. Note: Make sure you remember the name of your private key file and save it somewhere easily accessible.

### Create a Lambda Function

1. Navigate to Lambda.
2. Click Create a function.
3. Choose Author from scratch and use the following settings:
    * Name: CreateEC2
    * Runtime: Python 3.7
    * Role: Create a custom role
4. Expand Choose or create an execution role.
5. Set Execution role to Create a new role with basic Lambda permissions.
6. Copy the execution role name that appears (it will be something like CreateEC2-role-). Paste it into a text file for use later in the lab.
7. Click Create function.
8. In a new browser tab, navigate to IAM.
9. Click Roles in the left-hand menu.
10. In the search box, type in the name of the role we just created.
11. Select the role.
12. Click the policy that's currently attached to the role.
13. Click {} JSON.
14. Click Edit policy > JSON.
15. Paste in the following policy:

```
{
  "Version": "2012-10-17",
  "Statement": [{
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Action": [
        "ec2:RunInstances"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

16. Click Review policy and then Save changes.
17. Back in the Lambda console, refresh the page.
18. On the CreateEC2 function page, scroll down to the Function code section.
19. Paste in the Python source code from this file on GitHub.
20. Click Save.
21. Scroll down to the Environment variables section.
22. Set the following environment variables:

* AMI
    * Key: AMI
    * Value: Open EC2 in a new browser tab, click Launch Instance, and copy and paste the ami value listed after Amazon Linux 2.

* INSTANCE_TYPE
    * Key: INSTANCE_TYPE
    * Value: t2.micro

* KEY_NAME
    * Key: KEY_NAME
    * Value: The name of the EC2 key pair you created earlier.

* SUBNET_ID
    * Key: SUBNET_ID
    * Value: Navigate to VPC > Subnets, and copy and paste the ID of one of the public subnets in your VPC.

* Save the Lambda function.

### Test Lambda Function

1. Click Test.
2. Define an empty test event. Its contents can simply be {}.
3. Give it any name you like.
4. Click Create.
5. Click Test.
6. Navigate to EC2 > Instances, and observe that an EC2 instance is initializing.

### Connect to the Instance via SSH

1. Open a terminal session, and change directory to where your .pem file is saved from earlier.
2. Set the permissions of your private key file so that only you can read it:

```
chmod 400 <KEY PAIR>.pem
``` 
Replace <KEY-PAIR> with your key pair name.

3. Connect via the public IP of the EC2 instance:

```
ssh -i <KEY-PAIR>.pem ec2-user@<IP ADDRESS>
```

Remember to replace <IP ADDRESS> with the public IP of the EC2 instance you created.

Alternatively, you can get the connection command by clicking Connect with the instance selected in the EC2 console.