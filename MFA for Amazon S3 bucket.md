How do I turn on MFA delete for my Amazon S3 bucket?
I want to turn on multi-factor authentication (MFA) delete for my Amazon Simple Storage Service (Amazon S3) bucket to protect my objects from unintended deletions.

Short description
With Amazon S3 MFA delete, you can prevent accidental object deletions. If you don't turn on MFA delete, then anyone with either of the following permissions can permanently delete an Amazon S3 object:

Root user password
Credentials of a sufficiently privileged AWS Identity and Access Management (IAM) user or role 
After you turn on MFA delete, only the root user can permanently delete object versions or change the versioning configuration on your S3 bucket. The root user must be authenticated with an MFA device to perform this action.

Note: MFA delete is supported only on buckets where versioning is turned on. The bucket owner, AWS account that created the bucket, and all authorized IAM users can turn on versioning. However, only the bucket owner of the root account can turn on MFA delete. 

To turn on MFA delete for your bucket, complete the following steps:

Generate an access key and secret key for the root user.
Activate an MFA delete device for the root user.
Configure the AWS Command Line Interface (AWS CLI) with root user credentials.
Use the PutBucketVersioning API to turn on the MFA delete feature.
Confirm that MFA delete is working. Remember to delete your root access keys.
Note: If you receive errors when running AWS CLI commands, make sure that you’re using the most recent AWS CLI version.

Resolution
Generate an access key and secret key for the root user
Follow the instructions in Creating access keys for the root user.

After you create these keys, you get a warning that explains that you have only this one opportunity to view or download the keys. You can't retrieve these keys later. Therefore, make sure to save them for configuring the AWS CLI.

Activate an MFA device for the root user
If you don't have an MFA device that's activated for the root user, then follow the instructions in Enable a virtual MFA device for your AWS account root user (console).

If you already have an MFA device that's activated for the root user, then note the ARN.

Configure the AWS CLI with the root credentials
Run the AWS CLI configure command.

When prompted for the AWS Access Key ID (Example: AKIAEXAMPLEABCQWE), paste the access key ID of the root user that you downloaded in step 1.
When prompted for the AWS Secret Access Key of the root user, paste the secret access key ID. You can find this information in the file that contains the root user’s credentials.
(Optional) When promoted for the Default region name, you can skip and press Enter.
(Optional) When prompted for the Default output format, you can skip and press Enter.
Important: If you configured named profiles on the AWS CLI, then you must create another profile for the root user's credentials.

To configure a named profile, run the following command:

aws configure --profile root_user
Use the PutBucketVersioning API to turn on the S3 MFA delete feature
To turn on MFA delete, run the put-bucket-versioning command:

aws s3api put-bucket-versioning --bucket mybucketname --versioning-configuration MFADelete=Enabled,Status=Enabled --mfa "arn:aws:iam::(accountnumber):mfa/root-account-mfa-device (pass)"
Example:

aws s3api put-bucket-versioning --bucket mybucketname --versioning-configuration MFADelete=Enabled,Status=Enabled --mfa "arn:aws:iam::1XXXXXXX6789:mfa/root-account-mfa-device 123789"
In this example, 123789 is the six digit example code that's generated with the MFA device.

If you used a named profile for the root user, then run the following command:

aws s3api put-bucket-versioning --bucket mybucketname --versioning-configuration MFADelete=Enabled,Status=Enabled --mfa "arn:aws:iam::1XXXXXXX6789:mfa/root-account-mfa-device 123789" --profile root_user
If the command is successful, then you don't get an output. If you get an error, then make sure that you installed the latest version of the AWS CLI. Also, confirm that you're using the root user and that the MFA code and ARN are valid.

Confirm that MFA delete is working
Check the Amazon S3 console to make sure that Versioning is turned on for the bucket.

To check that MFA delete is turned on, use the GetBucketVersioning API:

aws s3api get-bucket-versioning --bucket mybucketname  
{  
    "Status": "Enabled",  
    "MFADelete": "Enabled"  
}
After you turn on MFA delete for the bucket, you must include the request header in all future object deletes to permanently delete an object version. The header's value is the chain of your authentication device's serial number, a space, and the authentication code displayed on it. For more information, see Deleting an object from an MFA delete-enabled bucket. You can use the --mfa option in the delete-object command to include the header value.

If you try to delete an object version in your bucket without MFA, then you get the following error. You also get this error when you use an IAM user to try to delete the object version:

aws s3api delete-object --bucket mybucketname --key myobjectkey --version-id 3HL4kqCxf3vjVBH40Nrjkd  
An error occurred (AccessDenied) when calling the DeleteObject operation: Mfa Authentication must be used for this request
To use the root user to delete an object version in a bucket with MFA delete turned on, run the following command:

aws s3api delete-object --bucket mybucketnme --key myobjectkey --version-id 3HLkqCxf3vjVBH40Nrjkd --mfa "arn:aws:iam::(accountnumber):mfa/root-account-mfa-device (pass)"  
{  
    "VersionId": "3HLkqCxf3vjVBH40Nrjkd"  
}
Note: IAM users or roles within your AWS account that have s3:DeleteObject permission can still make a successful delete-object request to objects within your bucket without specifying a version ID. In a bucket with versioning, this request creates a delete marker. The current version of the object persists as a previous version. The only way to permanently delete an object in these buckets is to specify the version ID of the object version in the delete-object request. With MFA delete turned on, only the root user can make a version-aware delete to objects in the bucket. Also, the root user must authenticate the request with the root user MFA device.

As an security best practice, after you turn on MFA delete, do the following:

Invalidate and remove the root user credentials that are stored on your AWS CLI. AWS CLI credentials are stored in the configuration folder for your operating system. For more information, see Configuration and credential file settings.
Use the AWS Management Console to delete the root user access keys. For instructions, see Deleting access keys for the root user.
Related information
Configuring MFA delete

source: https://repost.aws/knowledge-center/s3-bucket-mfa-delete 
