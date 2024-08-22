**SSH connection prerequisites**
Before you can connect to your Linux instance using SSH, complete the following tasks.

**Complete the general prerequisites.**
Check that your instance has passed its status checks. It can take a few minutes for an instance to be ready to accept connection requests. For more information, see View status checks.

Get the required instance details.

Locate the private key and set permissions.

(Optional) Get the instance fingerprint.

**Allow inbound SSH traffic from your IP address.**
Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address. For more information, see Rules to connect to instances from your computer.

**Install an SSH client on your local computer (if needed).**
Your local computer might have an SSH client installed by default. You can verify this by entering the following command in a terminal window. If your computer doesn't recognize the command, you must install an SSH client.

##########################################################################################################
**Connect to your Linux instance using an SSH client**
To connect to your instance using an SSH client
**Open a terminal window on your computer.**

**Use the ssh command to connect to the instance. You need the details about your instance that you gathered as part of the prerequisites. For example, you need the location of the private key (.pem file), the username, and the public DNS name 

(Public DNS) To use the public DNS name, enter the following command.
ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name**

The following is an example response.
The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (198-51-100-1)' can't be established.
ECDSA key fingerprint is l4UB/neBad9tvkgJf1QZWxheQmR59WgrgzEimCG6kZY.
Are you sure you want to continue connecting (yes/no)?

**(Optional) Verify that the fingerprint in the security alert matches the fingerprint. If these fingerprints don't match, someone might be attempting a man-in-the-middle attack. If they match, continue to the next step. For more information, see Get the instance fingerprint.**

**Enter yes.**
You see a response like the following:
Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (ECDSA) to the list of known hosts.

##########################################################################################################























