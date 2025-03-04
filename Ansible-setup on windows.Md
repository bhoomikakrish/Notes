✅ Corrected Method 1: Using .pem Key for Passwordless Authentication
This method allows passwordless SSH from your WSL (control node) to your AWS Ubuntu instance (managed node) using a private key.

Step 1: Install OpenSSH Client
Ensure OpenSSH is installed on WSL:
sudo apt update && sudo apt install openssh-client -y

Step 2: Check if SSH Agent is Installed
which ssh-agent
If it returns /usr/bin/ssh-agent, proceed to the next step.

Step 3: Start SSH Agent
eval "$(ssh-agent -s)"

Step 4: Add Your .pem Key
ssh-add ~/Downloads/HarishBhoomi.pem
✅ Note: This key must have 600 permissions, or SSH will reject it. Fix permissions if needed:
chmod 600 ~/Downloads/HarishBhoomi.pem

Step 5: Manually Copy the Public Key
Since .pem keys do not have a .pub file, use this method instead:
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@3.110.56.142
🚀 Alternative (if ssh-copy-id fails):
cat ~/.ssh/id_rsa.pub | ssh -i ~/Downloads/HarishBhoomi.pem ubuntu@3.110.56.142 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"

Step 6: Test Passwordless SSH
ssh ubuntu@3.110.56.142
If it connects without asking for a password, setup is successful! 🎉

Method 2: ✅ Corrected Method 2: Enabling Password-Based SSH (Without .pem Key)
----------------------------------------------------------------------->
This method allows password authentication instead of .pem files.

Step 1: Log into AWS EC2 Instance
If you already have access using your .pem key, connect:
ssh -i ~/Downloads/HarishBhoomi.pem ubuntu@3.110.56.142

Step 2: Edit SSH Server Configuration
sudo nano /etc/ssh/sshd_config
Find and modify the following lines:

PasswordAuthentication yes
PermitRootLogin prohibit-password

✅ Important: There is NO passwordless authentication option in SSH.
Save and exit (CTRL + X, then Y, then Enter).

Step 3: Restart SSH Service
sudo systemctl restart ssh

Step 4: Set a Password for Ubuntu User
sudo passwd ubuntu
Enter a new password when prompted.

Step 5: Enable Passwordless SSH Login
ssh-copy-id ubuntu@3.110.56.142
Enter the password when prompted.

Step 6: Test Passwordless SSH
ssh ubuntu@3.110.56.142
If you can log in without a password, it's working!

🎯 Final Notes
Method 1 is more secure because it uses SSH keys.
Method 2 is useful when .pem keys are unavailable, but it's less secure.
Always ensure port 22 (SSH) is open in AWS Security Groups.
