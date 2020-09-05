The setup is done through AWS EC2 instances.  

# Part 1 - Creating an EC2 instance  
Create a new EC2 instance named "Ansible_Server" based on the instructions at [1-Jenkins.md](https://github.com/atakanttl/simple-devops-project/blob/master/1-Jenkins.md).  

# Part 2 - Connect through SSH  
Connect to the "Ansible_Server" via SSH based on the instructions at [1-Jenkins.md](https://github.com/atakanttl/simple-devops-project/blob/master/1-Jenkins.md).  

# Part 3 - Install Ansible  
1. Install python & pip.  
`$ yum install python -y`  
`$ yum install python-pip -y`  
2. Intall Ansible.  
`$ pip install ansible`  
3. Create an ansible directory & hosts file.  
`$ mkdir /etc/ansible`  
4. Create an Ansible user.  
`$ useradd ansadmin`  
`$ passwd ansadmin`  
5. Add ansadmin user to sudoers.  
`$ visudo`  
At the end of the file, add:  
> ansadmin ALL=(ALL) NOPASSWD: ALL  
 
6. Install Docker and start Docker daemon.  
`$ yum install docker -y`  
`$ service docker start`  
7. Add ansadmin to Docker group.  
`$ usermod -aG docker ansadmin`  
8. Enable password authentication for SSH.  
`$ vi /etc/ssh/sshd_config`  
Find “PasswordAuthentication” and change the value from “no” to “yes”. Save & exit. Restart the ssh service.  
`$ service sshd reload`  
9. Switch to the ansadmin user.  
`su - ansadmin`  
9. Create an ssh key.  
`$ ssh-keygen`  
10. Become root again, change the hostname to ansible-control-node.  
`$ sudo hostname ansible-control-node`  
`$ sudo su -`  
11. Go back to docker host via SSH. Create a user called ansadmin.  
`$ useradd ansadmin`  
`$ passwd ansadmin # use the password you have chosen at step 4`  
`$ usermod -aG docker ansadmin`  
Add ansadmin to sudoers.  
`$ visudo`  
At the end of the file, add:  
    > ansadmin ALL=(ALL) NOPASSWD: ALL  

13. Go back to Ansible host again. Switch to ansadmin user. Copy ssh key to docker host - ansadmin user. It can connect to the private IP because these servers are on the same VPC.  
`$ ssh-copy-id ansadmin@"DOCKER-HOST-PRIVATE-IP"`  
14. Try to connect to the Docker host - ansadmin user via ssh in Ansible server.  
`$ ssh ansadmin@"DOCKER-HOST-PRIVATE-IP"`  
If it is successful, exit from ssh.  
15. Switch to the Ansible directory and create a file called hosts.  
`$ cd /etc/ansible`  
`$ sudo vi hosts`  
Get the IP address from the Docker host, add it to the hosts file.  
> "DOCKER-HOST-PRIVATE-IP"  
> localhost  

16. Copy the ssh to the localhost.  
`$ ssh-copy-id localhost`  
17. Make a ping test between Ansible and Docker hosts.  
`$ ansible all -m ping`  

# Part 4 - Integrate Ansible with Jenkins  
- Login to your Jenkins page as admin. Go to “Manage Jenkins” → “Configure System”. Under “Publish over SSH”, add a new SSH Server.  
> Name: ansible-server
> Hostname: <ANSIBLE-SERVER-PRIVATE-IP>
> Username: ansadmin  

- Check “Use password authentication”  
> Passphrase / Password: <ansadmin password>  

- Test the configuration.  
- Apply, save.  

# Part 5 - Create a new Ansible playbook  
1. Create a new directory inside Ansible server (as ansadmin user).  
`$ sudo mkdir /opt/docker`  
`$ sudo chown -R ansadmin:ansadmin /opt/docker`  
2. Create a “hosts” file inside /opt/docker.  
`$ vi hosts`  
> localhost  
> "DOCKER-HOST-PRIVATE-IP"  

3. Create a Dockerfile inside /opt/docker from the Dockerfile given in the github repo.  
4. Create two Ansible playbooks named “create-webapp-image.yml” and “create-webapp-project.yml” inside /opt/docker. Contents of those files are in the repo with the same names.  
5. Login to docker as root user in both “Ansible Server” and “Docker Host”.  
`$ docker login`  

# Part 6 - Create a new Jenkins Job
- Instructions are at Jenkins_Job file.  

# Part 7 - Launch webapp  
- You can launch the webapp by opening a browser and entering "DOCKER-HOST-PRIVATE-IP":8080/webapp


















