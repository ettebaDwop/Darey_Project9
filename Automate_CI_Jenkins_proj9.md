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
3. Github
4. Webhooks

### Implementation

#### INSTALL AND CONFIGURE JENKINS SERVER
#### Step 1 – Install Jenkins server
Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins”

![Screenshot (515)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/f83cb806-ed96-4c63-a588-205055baaa20)


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

![Screenshot (516)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/8449fd37-54b5-46ca-bc74-0467ee525160)


Make sure Jenkins is up and running

`sudo systemctl status jenkins`

![Screenshot (517)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/bd4534b1-1fb8-4982-80b7-5878e3fda365)

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group
        
![Screenshot (518)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/816551a8-2a86-484c-acc6-d45b5bff7be2)

Perform initial Jenkins setup.
From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

 ![Screenshot (509)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/c6b5e8cb-213a-4dec-9e50-c993f1d6e57f)


Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Then you will be asked which plugins to install – choose suggested plugins.

![Screenshot (519)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/bf734801-5861-4cb9-a313-960a9df4636e)

 Once plugins installation is done – create an admin user and you will get your Jenkins server address.

![Screenshot (520)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/a147600a-1142-44f0-a492-7657d9518e5e)


![Screenshot (521)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/ab739edf-e55b-4884-a530-4196c81c59fb)

#### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

First we will enable webhooks in our GitHub repository settings:

        

Go to Jenkins web console, click “New Item” and create a “Freestyle project”
        

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

 

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

 

Save the configuration and let us try to run the build. For now we can only do it manually.
Click “Build Now” button, if you have configured everything correctly, the build will be successfull and you will see it under #1

 

You can open the build and check in “Console Output” if it has run successfully.

If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

Click “Configure” your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

    

Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

 

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

 

You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`


## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH


