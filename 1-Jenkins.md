The setup is done through AWS EC2 instances.  
# Part 1 - Creating an EC2 instance  
1. Select “Services”, under the “Compute” tab, select “EC2”. After that click “Launch instance”.
2. Select “Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type”.
3. Select a free tier eligible instance type, e.g. “t2.micro”.
4. Leave the default settings at “Step 3: Configure Instance Details” and “Step 4: Add Storage”.
5. At “Step 5: Add Tags” choose “Name” as key, and “Jenkins_Server” as value.
6. At “Step 6: Configure Security Group”, select a new “security group name”, e.g. "Simple-SG”. Add a new rule “Custom TCP”, select “Port Range” 8080 as the Jenkins server runs on the port 8080.
7. Select “Launch” at Step 7. Choose an existing key pair and select the key pair you have created.

# Part 2 - Connect through SSH
1. After creating the EC2 instance, copy the “IPv4 Public IP”.
2. Launch MobaXterm (you can choose another SSH client like PuTTy but with MobaXterm, you don’t need to convert the .pem key.).
3. Start a new session, choose SSH. Remote host is the public IP you have copied, the username is “ec2-user”. Select “Use private key” and select your “.pem” key.

# Part 3 - Installing prerequisites  
1. Login as root.  
`$ sudo su -`  
2. Change hostname and relog as root.  
`$ hostname jenkins`  
3. To check java version:  
`$ java -version`  
Output should be similar to “java version “1.7.0_211””.  
4. Remove and reinstall java.  
`$ yum remove java-1.7.0*`  
`$ yum install java-1.8*`
5. Set the home path for java.  
`$ find /usr/lib/jvm/java-1.8* | head -n 3`  
Open “.bash_profile” and set the “JAVA_HOME” as below.  
> JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-<Java version which seen in the above output>  
> export JAVA_HOME  
> PATH=$PATH:$JAVA_HOME  

Save the file and exit & relog as root.  
6. Check the java version again.  

# Part 4 - Installing Jenkins  
1. Get the latest version of jenkins from https://pkg.jenkins.io/redhat-stable/ and install (Amazon Linux is compatible with redhat/centos version).  
`$ yum -y install wget`  
`$ sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`  
`$ sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key`  
`$ yum -y install jenkins`  
2. Run Jenkins service and make it start at boot.  
`$ service jenkins start`  
`$ chkconfig jenkins on`  
3. Paste your Jenkins_Server IP address to a browser and append “:8080” (default port which jenkins run).  
4. Unlock Jenkins with the provided guide.  
`$ cat /var/lib/jenkins/secrets/initialAdminPassword`  
Copy the password and paste it into the “Administrator Password”.  
5. Skip “Customize Jenkins” step.  
6. Click at the top right “admin”, and then select “Configure” at the left pane. Change the password, relog with the new password.  
7. At the home page, select “Manage Jenkins” → “Global Tool Configuration” → “Add JDK” and as Name, write “JAVA_HOME”. Go back into SSH client, write `$ echo $JAVA_HOME` and copy the path, paste it into the JAVA_HOME section at Jenkins page.  
8. Click Apply, Save.  

# Part 5 - Running the first Jenkins Job  
1. Create a “New Item” at the home page.  
2. Enter an item name as “First_test_job”, select “Freestyle project”.  
3. Provide a description.  
4. At the “Build” section, select “Add build step” and choose “Execute shell”.  
5. Write the command: `echo “This is a test job.”`.  
6. Apply, Save.  
7. Select “Build Now” at your job page.  
8. You can see the console log from the circle icon at the “Build History” section.  

# Part 6 - Install git and integrate it to Jenkins  
1. Install git.  
`$ yum install git -y`  
2. Open Jenkins page, select “Manage Jenkins” → “Manage Plugins” → “Available” → Search “github” and select “GitHub - This plugin integrates GitHub to Jenkins.” → Select “Install without restart”.  
3. “Manage Jenkins” → “Global Tool Configuration”. Find the “Git” section.  
`Name:	github`  
`Path to Git executable: /usr/bin/git`  
4. Apply, Save.  

# Part 7 - Maven Setup  
1. Download maven packages https://maven.apache.org/download.cgi onto Jenkins server. In this case, /opt/maven is used as the installation directory  
`$ mkdir /opt/maven`  
`$ cd /opt/maven`  
`$ wget https://ftp.itu.edu.tr/Mirror/Apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz`  
`$ tar -xvzf apache-maven-3.6.3-bin.tar.gz`  
`$ ls`  
Check if the zipped file is unzipped correctly (there are bin, boot, conf, lib, LICENSE, NOTICE, README.txt files) without the folder name “apache-maven-3.6.3”.  
2. Open “.bash_profile”  
> M2_HOME=/opt/maven/  
> M2=$M2_HOME/bin  
> PATH=<Existing_PATH>:$M2_HOME:$M2  

Save and exit & relog as root, check the maven version.  
`$ mvn --version`  
3. Open Jenkins page, select “Manage Jenkins” → “Manage Plugins” → “Available”. Search “maven”, select “Maven Integration”, “Maven Invoker” and then install without restart.  
4. “Manage Jenkins” → “Global Tool Configuration”. Find the “Maven” section. Uncheck “Install automatically”.  
> Name: M2_HOME  
> MAVEN_HOME: /opt/maven  

3. Apply, Save.  

# Part 8 - Create a Maven job  
1. Create a “New Item” at the home page. Select “Maven Project”. Go to your github repo, copy the clone link. Under “Source Code Management” select “Git”, paste the link to the “Repository URL”.  
2. Under the “Build” section:  
> Root POM: pom.xml  
> Goals and options: clean install package  

3. Apply, Save.  
4. You can execute the job with “Build Now”.  
5. You can see the workspace directory with  
`$ cd /var/lib/jenkins/workspace/`  























