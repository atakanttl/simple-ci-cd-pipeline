The setup is done through AWS EC2 instances.  

# Part 1 - Creating an EC2 instance  
Create a new EC2 instance named "Docker_Host" based on the instructions at [1-Jenkins.md](https://github.com/atakanttl/simple-devops-project/blob/master/1-Jenkins.md).  

# Part 2 - Connect through SSH  
Connect to the "Docker_Host" via SSH based on the instructions at [1-Jenkins.md](https://github.com/atakanttl/simple-devops-project/blob/master/1-Jenkins.md).  

# Part 3 - Install Docker  
1. Become root.  
2. Change hostname to “docker-host”, relog as root.  
3. Install docker.  
`$ yum install docker -y`  
4. Start docker daemon.  
`$ service docker start`  
Check the status of the service.  
`$ service docker status`  
5. Create a user called dockeradmin.  
`$ useradd dockeradmin`  
`$ passwd dockeradmin # enter a password`  
Check groups, there should be a “docker” entity.  
`$ cat /etc/group`  
6. Add a user to docker group to manage docker.  
`$ usermod -aG docker dockeradmin`  
Check dockeradmin id.  
`$ id dockeradmin`  
7. Test an “apache tomcat” image.  
`$ docker run -d --name tomcat-container -p 8080:8080 tomcat:latest`  
This runs the tomcat server in detached mode with the exposed port 8080.  
8. (Optional) If you encounter 404 error when you are connected to the IP:Port, you can try:  
`$ docker exec -it tomcat-container /bin/bash`  
Above command lets you access the shell of the container.  
Check “/usr/local/tomcat” “webapps” folder. If you get a 404 error, it should be empty. You can copy the files inside “webapps.dist” to the “webapps” folder. The error should be gone.  

# Integrate Docker with Jenkins  
1. Login to your Jenkins page as admin. Go to “Manage Jenkins” → “Manage Plugins” → “Available”. Search “Publish Over SSH - Send build artifacts over SSH”, install without restart.  
2. “Manage Jenkins” → “Configure System”. Find the “Publish over SSH” section. Add an SSH Server. Get the ip address of the docker host (under eth0:inet).  
 
 > Name: docker-host  
 > Hostname: "DOCKER-HOST-PRIVATE-IP"  
 > Username: dockeradmin`  
 
3. Select “Advanced”, “Use the password authentication”. Enter the “Passphrase / Password”.  
4. Login to your docker host. You should enable password based authentication.  
`$ vi /etc/ssh/sshd_config`  
Find “PasswordAuthentication” and change the value from “no” to “yes”. Save & exit. Restart the ssh service.  
`$ service sshd reload`  
5. Go back to the Jenkins page, select “Test Configuration”.  
6. Apply, Save.  
