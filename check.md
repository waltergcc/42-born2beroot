# Born2beRoot

## Install and Config Sudo and Fisrt Groups
- [ ] su -
- [ ] apt-get update -y
- [ ] apt-get upgrade -y
- [ ] apt install sudo
- [ ] usermod -aG sudo wcorrea- && getent group sudo
- [ ] sudo visudo 
- [ ] Find User privilege specification then type: wcorrea-  	ALL=(ALL) ALL
- [ ] Defaults	badpass_message="Password is wrong, please try again!"
- [ ] Defaults	passwd_tries=3
- [ ] Defaults	logfile="/var/log/sudo/sudo.log"
- [ ] Defaults	log_input, log_output
- [ ] Defaults	requiretty
- [ ] sudo groupadd user42
- [ ] usermod -aG user42 wcorrea- && getent group user42
- [ ] cd ~/../ && cd var/log
- [ ] mkdir sudo && cd sudo && touch sudo.log

## Install the other programs
- [ ] apt-get install git -y
- [ ] sudo apt install openssh-server
- [ ] apt-get install ufw
- [ ] sudo apt-get install libpam-pwquality
- [ ] apt-get install -y net-tools

## Setup SSH and UFW
- [ ] sudo systemctl status ssh
- [ ] sudo nano /etc/ssh/sshd_config
- [ ] Find this line #Port22 to Port 4242 && sudo grep Port /etc/ssh/sshd_config
- [ ] sudo service ssh restart
- [ ] sudo ufw enable && sudo ufw status numbered
- [ ] sudo ufw allow ssh
- [ ] sudo ufw allow 4242 && sudo ufw status numbered
- [ ] Change the Host Port and Guest Port to 4242 in Oracle VM
- [ ] sudo systemctl restart ssh
- [ ] Restart the VM
- [ ] In Ubuntu terminal $> ssh wcorrea-@127.0.0.1 -p 4242 || rm ~/.ssh/known_hosts

## Password Policy
- [ ] sudo nano /etc/pam.d/common-password
- [ ] password  requisite     pam_pwquality.so  retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
- [ ] sudo nano /etc/login.defs
- [ ] PASS_MAX_DAYS 30 > PASS_MIN_DAYS 2 > PASS_WARN_AGE 7 
- [ ] sudo reboot
- [ ] sudo chage --maxdays 30 --mindays 2 --warndays 7 my_user
- [ ] chage -l wcorrea-

## Monitoring
- [ ] cd /usr/local/bin/ && touch monitoring.sh
- [ ] chmod 777 monitoring.sh
- [ ] Access the VM by Ubuntu terminal
- [ ] Change the monitoring.sh file
- [ ] sudo visudo
- [ ] under %sudo add wcorrea- ALL=(ALL) NOPASSWD: /usr/local/bin/monitoring.sh
- [ ] sudo reboot
- [ ] sudo /usr/local/bin/monitoring.sh
- [ ] sudo crontab -u root -e
- [ ] At the file end: */10 * * * * /usr/local/bin/monitoring.sh

## Last or First Checks
- [ ] Check linux release $> lsb_release -a || cat /etc/os-release
- [ ] Check the partitions $> lsblk
- [ ] Check if sudo in on $> dpkg -l | grep sudo
- [ ] Check the hostname $> hostnamectl
- [ ] Check the password policy $> sudo chage -l username
- [ ] Check UFW $> sudo ufw status numbered
- [ ] Check SSH $> sudo systemctl status ssh
- [ ] Check sudo log $> cd /var/log/sudo/sudo.log | cat -e
- [ ] Check if user is on sudo $> getent group sudo
- [ ] Check if user is on user42 $> getent group user42
- [ ] Run monitoring - cd /usr/local/bin && bash monitoring.sh

## Before Submit 
- [ ] Turn off the VM
- [ ] Generate the signature.txt file $> sha1sum file.vdi
- [ ] Compress the VM folder $> tar -cf file.tar *
- [ ] Git push the txt file

## During evaluation:
- [ ] Create a new user $> sudo adduser username
- [ ] Create a new group $> sudo groupadd evaluating
- [ ] Add the user to new group $> sudo usermod -aG evaluating username
- [ ] Add the user to sudo $> sudo usermod -aG sudo username
- [ ] Check the change $> getent group evaluating
- [ ] Change the hostname $> sudo hostnamectl set-hostname name && sudo nano /etc/hosts
- [ ] reboot, see and see the change $> sudo reboot | hostnamectl
- [ ] Restore the original hostname
- [ ] Add the 8080 port $> sudo ufw allow 8080
- [ ] See the changes $> sudo ufw status numbered
- [ ] Delete the port added $> sudo ufw delete 4
- [ ] Connect to new user $> ssh new_user@127.0.0.1 -p 4242
- [ ] Try to connect to root (Must ne fail) - ssh hostname@127.0.0.1 -p 4242 
- [ ] Change time to 1 - sudo crontab -u root -e 
- [ ] Stop cron - sudo cronstop
- [ ] Restart cron - sudo cronstart

## Questions
### VM
	É uma maquina virtual. Podemos ter mais de uma maquina e sistema instalado em um mesmo computador. É importante para podemos fazer diferentes testes tanto na parte de hardware tanto de software.
### CentOS and Debian?
	Debian é mais user friendly e o CentOS é mais voltado para o enterprise
### Porque Debian?
	Segui a instrução do PDF do sistema mais recomendado para se iniciar
### Apt, Aptitude, Apparmor
	Apt e aptitude são advanced package tool para instalar e gerenciar programas. O apt só faz exatamente o que é passado pela linha de comando, enquanto o aptitude tem um gerenciamento autonomo melhor.
	Apparmor protege o sistema criando perfis de segurança para cada programa, restringindo os acessos a recursos do sistema que não são usados.
### LVM
	Gerencia as partições de forma dinamica. Cria um grupo para as partições onde aloca de forma dinamica o espaço, impedindo a necessidade de formatações do disco físico ao realocar espaço.
### UFW
	Permite configurar de forma simples o firewall
### SSH
	É uma tecnica de encriptação da cominicação entre host e client. A comunicação fica protegida em ambas as pontas.
### Cron
	Permite agendar programas ou scripts para rodarem a determinado tempo