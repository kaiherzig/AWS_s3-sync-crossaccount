aws-s3-sync-crossaccount

#Preparation
Note your Account ARN. If you are using an EC2 instance, get your sts id by
aws sts get-caller-identification

In this example it's arn:aws:sts::1234567890:assumed-role/EC2_S3IAM/i-0a1b2c3d4e

In source account open your S3 Bucket. In this example source-bucket

Edit Bucket policy and paste

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DelegateS3Access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:sts::1234567890:assumed-role/EC2_S3IAM/i-0a1b2c3d4e"
            },
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::source-bucket/*",
                "arn:aws:s3:::source-bucket"
            ]
        }
    ]
}

In destination Account create an IAM Role and attach it to your machine or user:
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::source-bucket",
                "arn:aws:s3:::source-bucket/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::destination-bucket",
                "arn:aws:s3:::destination-bucket/*"
            ]
        }
    ]
}

Now you can use
aws s3 sync s3://source-bucket s3://destination-bucket