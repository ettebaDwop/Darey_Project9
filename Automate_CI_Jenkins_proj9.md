# PROJECT 9
## Tooling Website Deployment Automation with Continuous Integration: Introduction to JENKINS
### Overview
In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub https://github.com/<yourname>/tooling is automatically updated to the Tooling Website.

### Task
To enhance the architecture prepared in Project 8 by adding a Jenkins server(see red rectangle). The server would help configure a job for  automatic deployment of  source codes changes from Git to NFS server.

![jenkins_CI](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/0fdfdb75-85be-4d14-b138-d453257cbea0)

### Prerequisites

1. Project 8
2. Jenkins Server

### Implementation

#### INSTALL AND CONFIGURE JENKINS SERVER
##### Step 1 – Install Jenkins server
Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”


Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless

#Install Jenkins

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
 /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

Make sure Jenkins is up and running

`sudo systemctl status jenkins`

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group
        

Perform initial Jenkins setup.
From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

 

Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Then you will be asked which plugins to install – choose suggested plugins.

 

Once plugins installation is done – create an admin user and you will get your Jenkins server address.



- CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH


