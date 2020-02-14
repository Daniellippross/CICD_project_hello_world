I began this project by first setting up my Jenkins server on AWS.

First I ssh into my aws instance and installed the resourses needed for Jenkins:

sudo apt-get update
sudo apt-get install default-jdk

I then set up the .bash_profile to set JAVA as the home path:

JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.212.b04-1.el8_0.x86_64
PATH=$PATH:$JAVA_HOME

Jenkins installation:

wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
echo deb https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
sudo apt-get update
sudo apt-get install jenkins

now the jenkins server is availiable on my ec2 instance on port 8080

This allows me to unlock jenkins with the admin password found using:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Once on the jenkins dash board I added the JAVA path to the gloval tools configuration.

I ran a simple test build by creating a freestyle project and under the build section I added an execute shell command:
echo "Welcome to my Devops project".

My next goal was to set up the git plugin on jenkins, I brought up my jenkins server cli and executed the following commands:

sudo apt update
sudo apt install git

I then returned to the Jenkins dashboard and installed the github plugin in manage plugins and the added to the global tools

Next I wanted to install and configure maven.

In my jenkins server cli I moved into the /opt directory and ran the commands:

sudo wget http://apache.mirror.anlx.net/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
sudo tar -xvzf apache-maven-3.6.3-bin.tar.gz
sudo mv apache-maven-3.6.3 maven

in the .profile I added the following variables and added them to the path:

M2_HOME=/opt/maven
M2=/opt/maven/bin

On the jenkins dashboard I installed Maven intergration and Maven invoker in the manage plugins and then added the maven path in global tools.

Next I wanted to make a test build with maven, I created the build and added my JAVA hello world github repo to the source code.

My next goal was to set up the tomcat server.

I created a new ec2 instance with the same config ans SG as the Jenkins server.

In the ec2 instance I installed apache-tomcat in the /opt directory:

sudo wget http://apache.mirror.anlx.net/tomcat/tomcat-8/v8.5.51/bin/apache-tomcat-8.5.51.tar.gz
sudo tar -xvzf apache-tomcat-8.5.51.tar.gz
sudo mv apache-tomcat-8.5.51 tomcat

I found it was necessey to install Java to run tomcat so I ran:

sudo apt-get update
sudo apt-get default-jre

I then ran tomcat with:

sudo bin/startup.sh

to check it was running i visted the ec2 ip wit the port 8080.

to allow deployment to the tomcat server I needed to add the deploy to container plugin
I then created a new job on jenkins to deploy to my tomcat server.

Next I wanted to find a way to deploy on a docker container. I began by launching a new ec2 instance to host docker on.
to install docker I ran the following commands:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
apt-cache policy docker-ce
sudo apt-get install -y docker-ce
