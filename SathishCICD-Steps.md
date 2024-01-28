##*DevOps Project Notes*
===
EC2-1 (t2.medium)
#1. Install Jenkins, GIT, Maven & Docker
  -> `amazon-linux-extras install epel -y (extra packages for enterprise linux)`
  -> `yum update -y`
  -> `wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo`
  -> `rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key`
  -> `amazon-linux-extras install java-openjdk11 -y`
  -> `yum install jenkins -y`
  -> `yum install git -y`
  -> `yum install docker -y`
  -> Start the docker and jenkins
  -> update the permission of docker file chmod 777 /var/run/docker.sock
  -> Install maven on /opt by using below link
       `sudo wget https://mirrors.estointernet.in/apache/maven/maven-3/3.8.5/binaries/apache-maven-3.8.5-bin.tar.gz`
  -> Configure Maven plugin and add home path of maven into plugin.
  -> Pass Docker credentials into global credential manager

---
EC2-2 (t2.medium)
#2. SonarQube
(Note: Never start sonar in root user)
   -> `sudo amazon-linux-extras install epel -y`
   -> `sudo amazon-linux-extras install java-openjdk11 -y`
   -> `sudo yum install mysql -y`
   -> `sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-6.7.6.zip`
   -> `sudo unzip sonarqube`
   -> create a subnetgroup and mysql 5.7.39 rds database
   -> `sudo update-alternatives --config java`
   -> `/usr/lib/jvm/java-11-openjdk-11.0.21.0.9-1.amzn2.0.1.x86_64/bin/java`
   -> edit wrapper.conf and add the above javahomepath
   -> edit sonar.properties configure db-username/password/endpointname/web.host/web.context.web.port
   -> `mysql -h database-1.cn246ew2c9fl.us-east-1.rds.amazonaws.com -P 3306 -u admin -p`
   -> Create username and password in mysql
==============================================================
*DB Commands to create users:*

CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;

CREATE USER sonar@localhost IDENTIFIED BY 'sonar';

CREATE USER sonar@'%' IDENTIFIED BY 'sonar';

GRANT ALL ON sonar.* TO sonar@localhost;

GRANT ALL ON sonar.* TO sonar@'%';
==============================================================
   -> `sudo chown ec2-user:ec2-user -R /opt/sonarqube-6.7.6`
   -> chown ec2-user:ec2-user /opt/sonarqube-6.7.6`
   -> ``Start the sonarqube ./sonar.sh start`
   -> http://sonarserverip:9000/sonar
   -> Login user: admin, Pass: admin
   -> Install Sonar plugin
   -> Pass Sonar Credentials into global credentials manager e7c6a0de3102c918429a03faa02d028dc0788d55
   -> Manage Jenkins > System > Add Sonarqube > http://18.212.66.2:9000/sonar

---
#3. GITHUB Webhook
   -> Install Github integration plugin
   -> Enable gitbub webhook trigger in jenkins
   -> Add payload URL in Webhook http://jenkinsip:8080/github-webhook/

---
#4. ECS
   -> Pass AWS Secret key and Access key into global credentials
   -> Create a ECR Registry into aws

---
EC2-3 (t2.medium)
#5. Nexus
   -> yum install java-1.8.0 -y
   -> wget https://download.sonatype.com/nexus/3/nexus-3.43.0-01-unix.tar.gz
   -> create docker private repo with port
   -> login to docker machine and edit daemon.json file

---
**Ref-link:**
https://blog.sonatype.com/using-sonatype-nexus-repository-3-part-3-docker-images
   
---
**Overall Stages:**

1. Stage1 SCM Check-Out
2. Stage2 Maven-Build
3. Stage3 SonarQube Analysis
4. Stage4 Build Docker Image
5. Stage5 Push Docker Image to DockerHub
6. Remove Old Container bfr deploying in to VM
7. Stage6 Store Docker images in Nexus Private Registery
8. Stage7 Deploy the docker image on VM

===





















