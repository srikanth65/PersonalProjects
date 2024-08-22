![image](https://github.com/user-attachments/assets/400988d0-a43a-48ed-8d9d-d463f4299718)

Using the `scp` command allows you to efficiently and securely transfer files from your Mac to your AWS EC2 server. This method ensures your data remains protected during the transfer.

**Step-by-Step Guide**

1. Open Terminal on Your Mac
- Why: Terminal is the command-line interface you’ll use to run the `scp` command.
- How: Find it in Applications > Utilities, or use Spotlight to search for it.

2. Navigate to the Directory with Your File
- Why: You need to be in the directory where the file you want to copy is located.
- How: Use the `cd` command. For example:
```cd /path/to/your/file```

3. Use `scp` Command to Copy the File
- Why: The `scp` command securely transfers files using SSH, ensuring encrypted communication.
- How:
```scp -i /path/to/your-key-pair.pem /path/to/local/file username@ec2-instance-public-dns:/path/to/remote/directory ```
— Replace placeholders as follows:
— `/path/to/your-key-pair.pem`: Path to your EC2 key pair file.
— `/path/to/local/file`: Path to the file you want to copy.
— `username`: Typically `ec2-user` for Amazon Linux, `ubuntu` for Ubuntu instances, etc.
— `ec2-instance-public-dns`: Public DNS of your EC2 instance.
— `/path/to/remote/directory`: Directory on the EC2 instance where you want to copy the file.

Example Command
Suppose your key pair file is in `~/.ssh/my-key-pair.pem`, the file you want to copy is `~/Documents/myfile.txt`, and you want to copy it to the home directory of an Amazon Linux instance with the public DNS `ec2–12–34–56–78.compute-1.amazonaws.com`:
```scp -i ~/.ssh/my-key-pair.pem ~/Documents/myfile.txt ec2-user@ec2–12–34–56–78.compute-1.amazonaws.com:~```

prerequiste: 
1. you must have YourKeyFile.pem
2. set permissions to " chmod 400 /path/to/yourKeyFile.pem"

**Few notes for beginning:**
Note the spaces between the three parameters given after the -i
scp stands for secure copy protocol. Knowing the words makes it easier to remember the command.
-i dictates that you need to give the .pem file as the next param. If there is no -i, than you do not need a .pem.
Note the :~ at the end of the destination for the EC2 instance.
########################################################################################

**Send file from Local to Server:**
scp -i .ssh/awsinstance.pem my_local_file ubuntu@XX.XXX.XXX.XXX:/home/ubuntu

**Download file from Server to Local:**
scp -i .ssh/awsinstance.pem ubuntu@XX.XXX.XXX.XXX:/home/ubuntu/server_file .

**if you want to copy a folder use the "-r" parameter -r (for directory)**
scp -r -i ~/pathToPemFile/AmazonKey.pem /Users/yourUserName/desktop/yourFolderName ec2-user@ec2-50-17-16-67.compute-1.amazonaws.com:~/.

scp -i /path/to/your/.pemkey -r /copy/from/path user@server:/copy/to/path
-r if it's a directory






