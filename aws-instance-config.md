# AWS Instance Configuration
## Purpose
The purpose of this readme is to help you configure a new AWS instance from scratch.

## Requirements
Proceed to these steps only once you have launched an AWS instance. See details [here](https://github.com/the-meyer-lab/aws-cloud-computing-setup/blob/main/aws-account-config.md).

## 1. Connect to instance from your command line
If you have a Max or a Linux machine, great open your terminal.
If you have a Windows machine, follow instructions on installing Bash terminal [here](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/).

- EC2 > Instances > select desired instance that has been launched and click "Connect"
	- Will display a sample command to connect to your instance e.g.:
	```bash
	ssh -i "G:\My Drive\Meyer Lab\AWS\yuri-malina.pem" ubuntu@ec2-54-219-137-86.us-west-1.compute.amazonaws.com
	```
	- "G:\My Drive\Meyer Lab\AWS\yuri-malina.pem" should be replaced by the path to your .pem file downloaded in step [[Nanopore Sequencing AWS Setup Manual#c Create key pair for users who will be accessing instances]].
		- "c2-54-219-137-86" should be replaced with the address listed on your instance's connect page.
	- If you get an error with this command, an alternative is:
	```bash
	ssh -i "G:\My Drive\Meyer Lab\AWS\yuri-malina.pem" ec2-c2-54-219-137-86.us-west-1.compute.amazonaws.com -l ubuntu
	```
### a. Quality of life (optional tools)
When working with a remote server, if you are not entirely comfortable with the command line interface, navigating around, manipulating files, and creating / editing text files with vim, nano or other CLI text editor, it is highly recommend to install some tools to improve your workflow:
- SFTP / FTP Client: *Allows you to view your local and remote file structure in a GUI, allowing for easy drag and drop file transfers.*
	- Windows:  [WinSCP](https://winscp.net/eng/index.php) is a good choice but there are others. 
	- Mac: [Cyberduck](https://cyberduck.io/)
- Integrated Development Environment: *Gives you a fully featured code editor, console window and file structure all in one interface*
	- Windows or Mac: I recommend [JetBrains Gateway](https://www.jetbrains.com/remote-development/gateway/) (free for .edu email addresses) but again there are others.

## 2. Configure your EBV drive on your AWS instance
Execute the following commands:

```bash
###### Configure EBV drive on instance  
# Format drives to the correct file system type, and mount them  
# Note: only do this when mounting drive for first time. This will clear data from an existing drive.

# Determine your drive name (usually of the format nvmeXnX) replace "nvme1n1" in all following commands with your drive name
# to list available drives: sudo lsblk

# For drives < 2TB in size:
printf "n\np\n\n\n\nw" | sudo fdisk /dev/nvme1n1  
# For drives > 2TB in size:
sudo mkfs -t xfs /dev/xvdf
# fdisk options: n = new partition; p = primary; accept 3 defaults; w = write  
sudo partprobe /dev/nvme1n1  
printf "y" | sudo mkfs.ext4 /dev/nvme1n1

# For copying large amount of files between drives: rsync -ah --info=progress2 [source] [destination]  
  
# Make folder (if it doesn't already exist) in your base level directory and mount drive. Data1 will be used as primary repository where EBS volumes will be mounted.
# If you have more than one EBS Volume, you may want to create additional Data folders as mounting points.
sudo mkdir -p /Data1  
sudo mount /dev/nvme1n1 /Data1  
# Change owner of drive  
sudo chown -R ubuntu /Data1  
######  
```

## 3. Setup up necessary software and packages.
If you set up your instance from an AMI, then you should be good to go with your project!

Otherwise you may need to first install necessary packages and software before proceeding.