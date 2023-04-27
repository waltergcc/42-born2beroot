# Born2beRoot
This project aims to create a virtual machine running linux that meets security and performance standards.

## Complete Guide
There is a great guide created by parqualeross : https://github.com/pasqualerossi/Born2BeRoot-Guide

## Install and sudo setup and First Groups
To ensure secure access, we installed sudo and created two groups: sudo and user42. We also edited the sudo configuration file using visudo and added the user to the sudo group. Additionally, we created the sudo.log file in /var/log/sudo.

```bash
su -
apt-get update -y
apt-get upgrade -y
apt install sudo
usermod -aG sudo your_username
getent group sudo
sudo visudo
Find User privilege specification then type: your_username   ALL=(ALL) ALL
sudo groupadd user42
usermod -aG user42 your_username
getent group user42
cd ~/../
cd var/log
mkdir sudo
cd sudo
touch sudo.log
sudo visudo
Defaults    badpass_message="Password is wrong, please try again!"
Defaults    passwd_tries=3
Defaults    logfile="/var/log/sudo/sudo.log"
Defaults    log_input, log_output
Defaults    requiretty
```

## Installing Additional Programs
To meet the project's performance requirements, we installed Git, OpenSSH-Server, UFW, libpam-pwquality, and net-tools.

```bash
sudo apt install openssh-server
sudo apt-get install ufw
sudo apt-get install libpam-pwquality
sudo apt-get install git -y
sudo apt-get install -y net-tools
```

## Setting up SSH and UFW
We changed the default SSH port from 22 to 4242 and enabled UFW. We also added a rule to allow traffic on port 4242 and opened the same port in the virtual machine's settings in Oracle VM. We restarted SSH, restarted the VM, and tested the connection.

```bash
sudo systemctl status ssh
sudo nano /etc/ssh/sshd_config
Find this line #Port22 to Port 4242 && sudo grep Port /etc/ssh/sshd_config
sudo service ssh restart
sudo ufw enable
sudo ufw status numbered
sudo ufw allow ssh
sudo ufw allow 4242
sudo ufw status numbered
Change the Host Port and Guest Port to 4242 in Oracle VM
sudo systemctl restart ssh
Restart the VM
In Ubuntu terminal
ssh wcorrea-@127.0.0.1 -p 4242
If get any error in the connection
rm ~/.ssh/known_hosts
change the Network config to attached to bridge adaptor - eno2
```

## Password Policy
We configured the password policy to meet the project's security requirements. We edited the common-password file to set the password retry limit to 3, password length to 10, and maximum repeated characters to 3. We set the password expiration policy using login.defs and chage commands. Lastly, we checked the password policy using chage.

```bash
sudo nano /etc/pam.d/common-password
password  requisite     pam_pwquality.so  retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
sudo nano /etc/login.defs
PASS_MAX_DAYS 30 > PASS_MIN_DAYS 2 > PASS_WARN_AGE 7
sudo chage --maxdays 30 --mindays 2 --warndays 7 your_username
sudo reboot
chage -l your_username
```

## Monitoring
We created a script named monitoring.sh in /usr/local/bin/ and set up a crontab job to run it every ten minutes. The script checks system logs, user activity, and memory usage.

```bash
cd /usr/local/bin/ && touch monitoring.sh
chmod 777 monitoring.sh
Access the VM by Ubuntu terminal
Change the monitoring.sh file
sudo visudo
under %sudo add wcorrea- ALL=(ALL) NOPASSWD: /usr/local/bin/monitoring.sh
sudo reboot
sudo /usr/local/bin/monitoring.sh
sudo crontab -u root -e
At the file end: */10 * * * * /usr/local/bin/monitoring.sh
```

## Last Checks
Before submitting the project, we performed some checks to ensure everything was set up correctly. These checks included verifying the Linux release, checking the partitions, verifying sudo installation, checking the hostname, checking password policies, checking UFW and SSH status, checking sudo logs, and verifying user group memberships.

```bash
lsb_release -a || cat /etc/os-release
lsblk
dpkg -l | grep sudo
hostnamectl
sudo chage -l username
sudo ufw status numbered
sudo systemctl status ssh
cd /var/log/sudo/sudo.log && cat sudo.log
getent group sudo
getent group user42
cd /usr/local/bin && bash monitoring.sh
```

After that we will turn off the VM, take a snapshot to keep the instance and generate a signature.txt get with the shasum of vdi.

```bash
sudo shutdown now
sha1sum born2beroot.vdi
cat > signature.txt
```

## Checklist and commands during the evaluation
- [ ] Check if sha1sum of vdi file is equal to signature.txt
- [ ] Check linux release `lsb_release -a || cat /etc/os-release`
- [ ] Check the partitions `lsblk`
- [ ] Check if sudo in on `dpkg -l | grep sudo`
- [ ] Check the hostname `hostnamectl`
- [ ] Check the password policy `sudo chage -l username`
- [ ] Check UFW `sudo ufw status numbered`
- [ ] Check SSH `sudo systemctl status ssh`
- [ ] Check sudo log `cd /var/log/sudo/` then `cat sudo.log`
- [ ] Check if user is on sudo `getent group sudo`
- [ ] Check if user is on user42 `getent group user42`
- [ ] Run monitoring `cd /usr/local/bin` then `bash monitoring.sh`
- [ ] Create a new user `sudo adduser username`
- [ ] Check the username `cd /etc/passwd | grep username`
- [ ] Create a new group `sudo groupadd evaluating`
- [ ] Add the user to new group `sudo adduser username evaluating`
- [ ] Add the user to sudo `sudo adduser username sudo`
- [ ] Check the changes `getent group evaluating` and `getent group evaluating`
- [ ] Change the hostname `sudo hostnamectl set-hostname newname` && `sudo nano /etc/hosts`
- [ ] reboot, see and see the change `sudo reboot` then `hostnamectl`
- [ ] Restore the original hostname `sudo hostnamectl set-hostname oldname` && `sudo nano /etc/hosts`
- [ ] Add the 8080 port `sudo ufw allow 8080`
- [ ] See the changes `sudo ufw status numbered`
- [ ] Delete the port added `sudo ufw delete 1-9`
- [ ] Check the ip address `sudo ip address`
- [ ] Connect to new user `ssh new_user@hostip.0.0.0 -p 4242`
- [ ] Try to connect to root (Must be fail) `ssh root@hostip.0.0.0 -p 4242` 
- [ ] Change time to 1min `sudo crontab -u root -e `

## Questions

### What is a VM and why is it important?
A VM is a virtual machine that allows multiple machines and operating systems to be installed on a single computer. It is important for conducting different tests on both hardware and software.

### What is the difference between CentOS and Debian?
Debian is more user-friendly, while CentOS is more focused on enterprise usage.

### Why did you choose Debian?
I followed the instructions from the recommended system PDF.

### What are Apt, Aptitude, and Apparmor?
Apt and aptitude are advanced package tools for installing and managing programs. Apt only does exactly what is passed on the command line, while aptitude has better autonomous management. Apparmor protects the system by creating security profiles for each program, restricting access to unused system resources.

### What is LVM?
LVM dynamically manages partitions by creating a group for partitions, allocating space dynamically, and avoiding the need for physical disk formatting when reallocating space.

### What is UFW?
UFW allows for simple configuration of the firewall.

### What is SSH?
SSH is a communication encryption technique between host and client. The communication is protected on both ends.

### What is Cron?
Cron allows for scheduling of programs or scripts to run at a specific time.

## Grade: 100 / 100