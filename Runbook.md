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

Instance type- t2.large

Open port 9000 to anywhere

Pass in user data

launch instance

#### Step 
Create Maven (in Jenkins) EC2 Instance

AMI - Amazon Linux 2023 24.04

Instance type- t2.large

Open port 9000 to anywhere

Pass in user data

launch instance