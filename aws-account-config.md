# AWS Account Setup and Configuration
## Purpose
 The purpose of this readme is to help you setup and configure a new AWS account and/or instance.

## AWS Account setup
*(skip if you already have an AWS account and key pair)*

### 1. Set up AWS account (if applicable)
Visit https://aws.amazon.com/ to create a new account if the lab does not have an active acccount.
  
### 2. Create key pair for that will be used to access remote instances
*(This is only required once for each user, can only be performed by AWS administrator and must be kept private.*)
- EC2 > Key pairs >
    - Enter name of user key pair will be granted to
    - Leave defaults: RSA / .pem
    - Download file, and securely provide to user who will be using it. Delete it once transfer is complete.

This key pair will be used whenever configuring a new AWS instance.

Click [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) for more detailed instructions.
	
## Configure AWS services
*(These steps can be skipped if your EC2 instances, EBV and S3 buckets are already configured.)*
### 1. Configure a new IAM role that will be granted to EC2 instances allowing for S3 access:
*(This is only required once when setting up AWS account for first time.)*
- IAM > Create role > AWS Service
    - Select use case, in this case allowing EC2 to call AWS services on our behalf.
- Permissions:
    - Select 'AmazonS3FullAccess'
- Name role 'EC2toS3' 
- Create role
- 
Click [here](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-instance-access-s3-bucket/) for more detailed instructions.

### 2. Set up an Elastic Block Storage volume 
An Elastic Block Volume, also known as an EBV, is a hard drive that will be attached to our compute instance for storage.

- EC2 > Elastic Block Store > Volumes > Create Volume
- Select volume type (General Purpose SSD is suitable for most applications)
- Select desired size
- Select region (needs to match region where you will be using instance.) For Meyer Lab applications, select us-west-1b

Click [here](https://aws.amazon.com/ebs/pricing/) for detailed pricing and performance information on the different types of EBVs.

### 3. Set up compute instance
- If desired instance is already configured, select from list and launch. If not configure new instance with following steps:
    - EC2 > Instances > Launch Instance > 
        - Select operating system / image: recommend Deep Learning AMI (Ubuntu 18.XX) Version XX.XX OR an available AMI from the lab for your specific application.
        - Select instance type:
            - For compute intensive workloads (e.g. base calling), recommend: g4dn.metal @ ~$9/hr
            >Note: When configuring a g4dn.metal instance for the first time, AWS default commercial accounts have a quota of 0 for these types of instances which will cause an error when attempting to launch the instance. In order to resolve this, visit Service Quotas > EC2 > request quota increase for "All G and VT Spot Instance Requests" up to '96' (which is the amount required by a g4dn.metal instance). 
            - For less intensive general purpose workloads (e.g. alignment), recommend:  t3a.2xlarge @ 	$0.39/hr
        - Configure instance details: 
            - Pick subnet that matches the region of the EBV. For Meyer Lab applications select us-west-1b
            - Assign IAM role "EC2toS3"
        - Review & Launch > Launch
            - Assign key pair of users who will be accessing instance. Create new key pair if required by following [[Nanopore Sequencing AWS Setup Manual#c Create key pair for users who will be accessing instances]]

Click [here](https://aws.amazon.com/ec2/pricing/on-demand/) for on-demand EC2 instance pricing and performance.

### 4. Attach EBV to instance
- EBS > Volumes
	- Select desired volume. If none exists see steps described above.
	- Actions > Attach Volume >
		- Select instance

### 5. Set up S3 bucket
S3 storage is generally recommended for long-term archival storage, that needs to be accessed by many instances or 3rd parties simultaneously.

- S3 > Create bucket
    - Add bucket name. Recommend a new bucket for each project.
    - Select region, ensure matches previously selected regions of instances & EBV.
    - Select whether versioning is desired. Disabled by default is fine.
        -  See standard S3 pricing [here](https://aws.amazon.com/s3/pricing/) to help you decide whether to use versioning. Versioning greatly increases storage utilization, see explanation [here](https://aws.amazon.com/s3/faqs/).
    - Create bucket.

#### a. Uploading your data to s3
- From the AWS console, navigate to your S3 bucket created in [[Nanopore Sequencing AWS Setup Manual#e Set up S3 bucket]]
- Click "Upload" and "Upload Folder"
- Select folder containing your data for uploading.