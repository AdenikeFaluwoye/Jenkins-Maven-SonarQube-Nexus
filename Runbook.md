#### Step 1 
Create Nexus EC2 Instance

AMI - Ubuntu 24.04

Instance type- t2.large

Open port 8081 to anywhere

Pass in user data

launch instance

Wait for like 5 minutes for Nexus to start running

Copy Nexus Public IP

get your admin password with 

sudo cat /opt/sonatype-work/nexus3/admin.password 

create new password 

create a repository 

#### Step 2
Create SonarQube EC2 Instance

AMI - Ubuntu 24.04

Instance type- t2.medium

Open port 9000 to anywhere

Pass in user data

launch instance

Log into Sonarqube-- Public IP:9000
USERNAME-admin 
PASSWORD-admin

Create project and token

Copy Comand and paste in your Jenkins file

#### Step 3
Create jenkins-Maven EC2 Instance

AMI - Amazon Linux 2023 24.04

Instance type- t2.large

Open port 9000 to anywhere

Pass in user data (maven-jenkins-ansible-Amazonlinux2023)

launch instance

copy public Ip and past on browser---- Public IP:8080

ssh into instance 

### To get Jenkins password
sudo cat LINK FROM JENKINS URL 

log in to Jenkins

### Install Maven
sudo dnf update -y
sudo dnf install maven -y
mvn -version

## Configure MAVEN_HOME and PATH Environment Variables
rm .bash_profile
wget https://raw.githubusercontent.com/awanmbandi/realworld-cicd-pipeline-project/jenkins-master-client-config/.bash_profile
source .bash_profile
mvn -v

# Create ".m2" and download your "settings.xml" file into it to Authorize Maven
## Make sure to Update the RAW GITHUB Link to your "settings.xml" config
mkdir /var/lib/jenkins/.m2
wget https://raw.githubusercontent.com/AdenikeFaluwoye/Jenkins-Maven-SonarQube-Nexus/refs/heads/main/settings.xml -P /var/lib/jenkins/.m2/
chown -R jenkins:jenkins /var/lib/jenkins/.m2/
chown -R jenkins:jenkins /var/lib/jenkins/.m2/settings.xml
