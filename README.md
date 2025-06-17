Ubuntu Server Migration to AWS Using AWS CLI – Step-by-Step Summary
________________________________________

1. Prepare the VMDK Image
	- Export the Ubuntu server image. Use .ovf format
	- Save or copy it to your local machine (e.g., Ubuntu-disk001.vmdk).
________________________________________

2. Upload VMDK to S3
	- Create an S3 bucket (e.g., server-migration123).
	- Upload the .vmdk file to this bucket.
________________________________________

3. Create “container.json” file locally using any text editor. This file defines the metadata for the import machine. 
 

	```json
		[
			{
				"Description": "ubuntu",
				"Format": "vmdk",
				"UserBucket": {
				"S3Bucket": "my-im-ex",
				"S3Key": "Ubuntu-disk001.vmdk"
				}
			}
		]
		```
________________________________________

4. Create an IAM Role for VM Import/Export
	- Create a role named myImpExpRole with custom trust policy and permission policies for the vmimport service:
 	 ```json
	 {
			"Version": "2012-10-17",
			"Statement": [
				{
					"Sid": "Statement1",
					"Effect": "Allow",
					"Principal": {
			    		"Service": "vmie.amazonaws.com"
						},
					"Action": "sts:AssumeRole"
				}
			]
	 }
	 ```
	- Trust policy: Allows EC2 to assume the role.
	- Permissions policy: Grants access to the S3 bucket and VM import service.
________________________________________

5. Install AWS CLI
	- Download and install the AWS CLI from the official AWS documentation.
	- Choose the version based on your OS (e.g., Windows, Linux, macOS).
________________________________________
6. Configure AWS CLI
	- Run the following command in your windows poweshell/terminal to set up CLI credentials:
		bash
 		aws configure
 		You'll be prompted to enter:
		- AWS Access Key ID
		- AWS Secret Access Key (shown only once—save it securely)
		- Default Region (e.g., us-east-1)
		- Default Output Format (e.g., json)
________________________________________

7. Run the EC2 Import Command
	- Execute the following AWS CLI command:
		bash
		aws ec2 import-image --disk-containers "file:<path of container.json>" --role myImpExpRole
________________________________________
8. Monitor Import Status

	- The CLI will return an ImportTaskId (e.g., import-ami-0fb048317993447a4). Monitor its status:
		bash
		- aws ec2 describe-import-image-tasks --import-task-ids import-ami-0fb048317993447a4
		You will see status like:
		- pending
		- active
		- completed
Once completed, an AMI will be created in your AWS account.
________________________________________
✅ Result
	- You now have a custom AMI created from your Ubuntu .vmdk file, ready to launch as an EC2 instance in your AWS environment.

