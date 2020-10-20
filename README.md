# simple-devops-project
This repository contains the steps to reproduce a [simple webapp project](https://github.com/atakanttl/simple-web-app).  
All servers are built inside the AWS EC2 instances.  

1. Jenkins fetches the changes on the [simple-web-app repository](https://github.com/atakanttl/simple-web-app). If there is a change on the files, it copies a .war file it has built into the Ansible server.
2. Ansible creates a Docker image and pushes it to the Dockerhub.
3. Ansible pulls the image from the Dockerhub and runs a container inside the Docker host machine.


The steps are given in [1-Jenkins.md](https://github.com/atakanttl/simple-devops-project/blob/master/1-Jenkins.md), [2-Docker.md](https://github.com/atakanttl/simple-devops-project/blob/master/2-Docker.md), [3-Ansible.md](https://github.com/atakanttl/simple-devops-project/blob/master/3-Ansible.md).  
The items needed for this project is given in [files](https://github.com/atakanttl/simple-devops-project/tree/master/files) directory.
