# Delegate Access to S3 using the AWS CLI
## Ensure that you are connecting to the US-East-1 region

## Exercise 1: Create IAM Role for EC2

1. Create an IAM role with a trust policy that uses the EC2 use case (or use the code below)
2. Attach the 'AmazonS3FullAccess' permissions policy
3. Name the role 'sts-assumerole-test'

## Exercise 2: Launch instance and assume role

### Create a Security Group

1. Create a security group
aws ec2 create-security-group --group-name IAMLabs --description "Temporary SG for the IAM Labs"
2. Add a rule for SSH inbound to the security group
aws ec2 authorize-security-group-ingress --group-name IAMLabs --protocol tcp --port 22 --cidr 0.0.0.0/0

### Launch Instance

1. Launch EC2 instance
aws ec2 run-instances --instance-type t2.micro --image-id ami-0440d3b780d96b29d --region us-east-1 --security-group-ids <SECURITY-GROUP-ID>
2. Describe EC2 instance
aws ec2 describe-instances --instance-ids <INSTANCE-ID>

### Create and Attach Instance Profile

3. Create instance profile
aws iam create-instance-profile --instance-profile-name s3-instance-profile
4. Add role to instance profile
aws iam add-role-to-instance-profile --role-name sts-assumerole-test --instance-profile-name s3-instance-profile
5. Connect to EC2 Instance Connect and run the same S3 test commands as earlier. They should fail
6. Attach instance profile to EC2 instance
aws ec2 associate-iam-instance-profile --iam-instance-profile Name=s3-instance-profile --instance-id <INSTANCE-ID>
7. Using EC2 instance connect, run the same S3 test commands as earlier. They should now work successfully

## Cleanup

6. Terminate EC2 instance
aws ec2 terminate-instances --instance-ids <INSTANCE-ID>
7. Remove role from instance profile
aws iam remove-role-from-instance-profile --role-name sts-assumerole-test --instance-profile-name s3-instance-profile
8. Delete instance profile
aws iam delete-instance-profile --instance-profile-name s3-instance-profile

***Trust relationship for role (for EC2)***

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```
