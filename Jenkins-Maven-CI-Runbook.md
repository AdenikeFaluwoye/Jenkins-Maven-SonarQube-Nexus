#### Step 1 
Create Nexus EC2 Instance

AMI - Ubuntu 24.04

Instance type- t2.large

Open port 8081 to anywhere

Pass in user data {Nexus-Instllation-T2.Large.sh}

launch instance

Wait for like 5 minutes for Nexus to start running

Copy Nexus Public IP

get your Nexus password with;
sudo cat /opt/sonatype-work/nexus3/admin.password 

Log in and create new password 

create a repository for release and snapshot under Maven2 Hosted(or a required type)

Copy the repository names and update them in Jenkins Credential

Copy the Public IP of the instance and update them in Jenkins Credential

#### Step 2
Create SonarQube EC2 Instance

AMI - Ubuntu 24.04

Instance type- t2.medium

Open port 9000 to anywhere

Pass in user data (Sonarqube-latest-installation.sh)

launch instance

Log into Sonarqube-- Public IP:9000
USERNAME-admin 
PASSWORD-admin

Create project and token

Copy Sonar token and needed details, sonarQube public IP and paste in your Jenkins Credential

#### Step 3
Create jenkins-Maven EC2 Instance

AMI - Amazon Linux 2023

Instance type- t2.large

Open port 8080 to anywhere

Pass in user data (maven-jenkins-ansible-Amazonlinux2023)

launch instance

ssh into instance 

sudo su

# Create ".m2" and download your "settings.xml" file into it to Authorize Maven
## Make sure to Update the RAW GITHUB Link to your "settings.xml" config
mkdir /var/lib/jenkins/.m2
wget https://raw.githubusercontent.com/AdenikeFaluwoye/Jenkins-Maven-SonarQube-Nexus/refs/heads/main/settings.xml -P /var/lib/jenkins/.m2/
chown -R jenkins:jenkins /var/lib/jenkins/.m2/
chown -R jenkins:jenkins /var/lib/jenkins/.m2/settings.xml

copy public Ip and past on browser---- Public IP:8080

### To get Jenkins password
sudo cat LINK FROM JENKINS page 

Copy and paste password and log in to Jenkins

Install suggested plugins

Fill in new login credentials

Click on New Item ---> Fill in project name

Choose either Free style project or Pipeline (depending on the route you're taken)

Under General 
 ---select GitHub project ---> paste ypur project repository Url copid from  your GitHub

 Under Triggers
  ---select GitHub hook trigger for GITScm polling

Under Pipeline >>> Definition 
 ---select  Pipeline script from SCM
Under SCM
 --- Select Git
Paste the project repository URL under repositories 
No credential needed since the Repository is public.
Under Branches to build
 --- Select */main

Apply the save

Go to Manage Jenkins --->Plugins ---> Available Plugins ---> Add neccessary plugins; slack, blue Ocean, Pipeline stage view the Install

### Intergrate slack with Jenkins
Go to your Slack App

Click on your already created channel name

Next Click on the heading of the name

Click on integrations ---> Add an App ---> Select Jenkins ---> Configuration ---> Add to Slack

Select the channel where Jenkins notifications will be posted ---> Add Jenkins CI integration

Scroll to Step 3 
 ---Copy Team Subdomain value
 ---Copy Integration Token Credential ID value


Now g to your Jenkins URL ---> Manage Jenkins ---> System 

Scroll to the bottom of the page then fill in Slack details
-- Under Workspace input the Team Subdomain value
-- Under Credentials; Add Jenkins
     -- In the pop up page;
       --  Under Kind, select Secret text
       -- Under Secret, input  Integration Token Credential ID value
       -- Under ID and Description, you can inpute Slack-tokenn ---> Add

Now youre back to the previous page
Under Credentials ---> Add ---> Select 'Slack-tokenn" from the dropdown
Test Connection ---> If success appears the apply and save
Go back to save configuration settings on your Slack app.

Go to Jenkins Home page ---> Click on your project ---> Build Now





