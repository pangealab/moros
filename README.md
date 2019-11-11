![Intro](./docs/manageiq-ui.jpg)

[ManageIQ](https://www.manageiq.org) open source cloud management platform for AWS. It was founded by Red Hat as a community project in 2014, and forms the basis for its CloudForms product. We will use this project to investigate how it performs discovery on AWS, OpenShift/K8s and compare with ServiceNow discovery. We will download the latest ManageIQ AWS baseline which is distributed as an [AWS AMI Appliance ](http://releases.manageiq.org/manageiq-ec2-ivanchuk-1.zip) and comes complete with its own pre-configured PosgreSQL database.

# Prerequisites

* AWS Console Access
* Existing VPC

# Installation

1. Create an S3 Bucket to stage the Appliance using your Profile (e.g. advlab)

    ```
    aws s3 mb s3://advlab-manageiq-bucket --profile advlab
    ```

1. Create a temporary EC2 Server called "filetransfer" on "DevOps Demo VPC" as "t2.micro" with a "16G" EBS Disk

1. SSH to "filetransfer"

1. Download Latest Release for AWS (e.g. Ivanchuk 2.2 GB)

    ```
    wget http://releases.manageiq.org/manageiq-ec2-ivanchuk-1.zip
    ```

1. Install some tools we may need

    ```
    sudo amazon-linux-extras install -y epel
    sudo yum install -y pv
    ```

1. Configure AWS Profile and provide your AWS Keys

    ```
    $ aws configure
    AWS Access Key ID [None]: ****
    AWS Secret Access Key [None]: ****
    Default region name [None]: us-east-2
    Default output format [None]: text
    ```

1. Copyt the ManageIQ Image to the S3 Bucket (9.2 GB Unzipped)

    ```
    unzip manageiq-ec2-ivanchuk-1.zip
    aws s3 cp manageiq*.vhd \
       s3://advlab-manageiq-bucket --expected-size=$((1024*1024*1024*10))
    ```

1. Terminate the temporary "filetransfer" instance

1. Create AWS Role

    ```
    aws iam create-role --role-name vmimport --assume-role-policy-document file://vm-import.json --profile=advlab
    ```

1. Edit AWS role-policy.json and set Resource to your S3 Bucket (e.g. advlab-manageiq-bucket)

    ```
    "Resource": [
        "arn:aws:s3:::advlab-manageiq-bucket",
        "arn:aws:s3:::advlab-manageiq-bucket/*"
    ```

1. Create AWS policy

    ```
    aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document file://role-policy.json --profile=advlab
    ```

1. Edit Container File (container.json) and set

    ```
    S3Bucket: (e.g. advlab-manageiq-bucket)
    S3Key: (e.g. manageiq-ec2-ivanchuk-1-201909111431-9f959bdc02.vhd)
    ```




