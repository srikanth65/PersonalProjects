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
