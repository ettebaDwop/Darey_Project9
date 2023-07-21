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
In our browser we can access Jenkins by using  the address: http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

Provide a default admin password

 ![Screenshot (509)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/c6b5e8cb-213a-4dec-9e50-c993f1d6e57f)


Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

Install the recommended plugins.

![Screenshot (519)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/bf734801-5861-4cb9-a313-960a9df4636e)

 Create an admin user and retrieve the Jenkins server address.

![Screenshot (520)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/a147600a-1142-44f0-a492-7657d9518e5e)


![Screenshot (521)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/ab739edf-e55b-4884-a530-4196c81c59fb)

#### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
In this part,we will configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

First, enable webhooks in  the GitHub repository settings:

![Screenshot (539)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/17f06e82-c16a-4bb7-801a-5f3b33f462ba)
        
![Screenshot (544)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/6f8671e1-29b4-49f1-9155-787783d5e8c7)

Go to Jenkins web console, click “New Item” and create a “Freestyle project”

![Screenshot (545)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/81b42a89-59c4-4d37-a9d3-f8c0f9d10078)
      
To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![Screenshot (558)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/a65825dc-5613-4b69-800f-51969d3a4e34) 
![Screenshot (548)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/58b332a2-622c-4c78-b796-a9f70e222ccc)


Run the build. This for now we can be done manually.
Click “Build Now” button, build shows under#4 (usually #1)

![Screenshot (549)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/55d92760-ec5f-4119-adcc-b026ffc5d7aa)

You can open the build and check in “Console Output” if it has run successfully.

![Screenshot (561)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/22608d17-a33a-485c-b839-9160e17829f5)

This build runs only when we trigger it manually.To change/ automate this:

Click “Configure” your job/project and add these two configurations
Configure triggering the job from GitHub webhook:

![Screenshot (551)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/5cc6ba47-6bed-4cda-a543-775576c446ff)

Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

![Screenshot (552)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/938e90e9-59be-4bea-aa15-95838ba87e0d)

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

![Screenshot (554)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/12d4de46-1949-4a43-ab64-9533bfc27244)

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

![Screenshot (553)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/5377cc83-1d6f-4fca-bf37-234a75fac631)
 
This shows that the build has been configured to run automatically.

By default, the artifacts are stored on Jenkins server locally. To see this run:

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`    # replace *<build_number>* with the build numbe in Jenkins

![Screenshot (556)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/9902c1f0-12da-403f-b59a-85883cfed711)



## CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
#### Step 3 – Configure Jenkins to copy files to NFS server via SSH

The artifacts have been  saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.To do this we will need a plugin that is called *“Publish Over SSH”.*

1. Install “Publish Over SSH” plugin.
   
a. Go to the main dashboard and select  “Manage Jenkins” and choose “Manage Plugins” menu item.

b. On “Available” tab search for “Publish Over SSH” plugin and install it:

![Screenshot (576)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/a9bfa070-10b4-4db9-bc28-9cc9973695ca)

2. Configure the job/project to copy artifacts over to NFS server.
   
 - On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:
![Screenshot (579)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/b432da9f-2dbc-45be-a9bc-be55d95ec371)

- Provide a private key (content of .pem file used to connect to NFS server)
- Arbitrary name
- Hostname – can be private IP address of your NFS server
- Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
- Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
- Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.
- 
![Screenshot (580)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/cc0f3f87-3ead-4863-970e-4e464137e4fd)

![Screenshot (578)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/7a9ce761-9e67-4671-910d-f9bffc29ffcb)


Save the configuration, open the Jenkins job/project configuration page and add another one “Post-build Action”

 Configure it to send all files produced by the build into our previously define remote directory. In our case we want to copy all files and directories – so we use **.

![Screenshot (580)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/5a7a9a12-a8c0-4230-8a0b-2673cc2213db)

If we make a change in the README.MD file in our GitHub Tooling repository.

![Screenshot (582)](https://github.com/ettebaDwop/Darey_Project9/assets/7973831/d32b0f15-a0b4-4cd0-b6ae-f0d21e748059)

Webhook will trigger a new job and in the “Console Output” of the job you will and we are likely to find something like this:

```
SSH: Transferred 25 file(s)
Finished: SUCCESS
```

To make sure that the files in /mnt/apps have been updated – connect via SSH(or Putty) to the NFS server and check README.MD file

`cat /mnt/apps/README.md`

The changes made previously in my GitHub indicates that the job works as expected.

