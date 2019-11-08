To deploy jenkins using this ansible script make sure you 
have ansible installed on the controller node or local machine

#################################################
## Controller & Target node pre-configurations ##
#################################################

## If you have not setup ssh keys on your system already ##

## Debian & Ubuntu Controller Node##
# Become Sudo user
sudo su

# Change to root .ssh folder
cd ~/.ssh

# Create keys 
ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"

# Save key as default provided
Enter file in which to save the key (/home/yourusername/.ssh/id_rsa):

# Leave passphrase empty - If you do decide to use
# a passphrase you may need to setup keychain to
# stop needing to enter your password for every deploy
Enter passphrase (empty for no passphrase):

At this point you will now need to configure the target node
to accept ssh from root user using ssh keys only



## Debian & Ubuntu target node ##
# Become Sudo user
sudo su

# create sudo password - you will be prompted to enter a password
passwd

# Open ssh config file
vim /etc/ssh/sshd_config

# Un-comment this line to look the same as this
From:  #PermitRootLogin prohibit-password
To: PermitRootLogin: yes

Then push [esc] wq [enter]

# Relaod ssh setting on target node
sudo service sshd restart 
OR 
sudo /etc/init.d/sshd restart



## Debian & Ubuntu controller node ##
# copy ssh key over to target node
ssh-copy-id root@<target-server-ip-address or fqdn>

# test your ssh connection is working with certificates
ssh root@<target-server-ip-address or fqdn>



## Debian & Ubuntu target node ##
# Time to secure the ssh connection
vim /etc/ssh/sshd_config

# Modify the following lines
From: PermitRootLogin yes
To: PermitRootLogin prohibit-password

From: #PubkeyAuthentication yes
To: PubkeyAuthentication yes

From: #PasswordAuthentication yes
To: PasswordAuthentication no

**From this point on you will only be able to access
the server from certificate based ssh**

# Relaod ssh setting on target node
sudo service sshd restart
OR
sudo /etc/init.d/sshd restart

# Install python & python3
apt-get install python python3 -y



## Debian & Ubuntu controller node ##
# Add your host to the host file
vim /etc/ansible/hosts

# Add the following to your host file: #
[jenkins]
jenkins ansible_host=<ip of target node> ansible_user=root

Once you have added these lines agin [esc] wq [enter]

# And now you can run the ansible playbook
cd <dir of ansible playbook> 
ansible-playbook deployJenkins.yml -v

** Make sure you use -v for verbose as the Initial Administration
password for jenkins will be printed in the output in the second last play
and you will need this to finish the configuration in the jenkins UI **



