# simple-devops-project
This repo contains the steps to reproduce a simple webapp project (https://github.com/atakanttl/simple-web-app).
> 
1. Jenkins fetches the changes on the simple-web-app repo. If there is a change on the files, it copies a .war file it has built into the Ansible server.
2. Ansible creates a Docker image and pushes it to the Dockerhub.
3. Ansible pulls the image from the Dockerhub and runs a container inside the Docker host machine.



`<addr>` element here instead.
