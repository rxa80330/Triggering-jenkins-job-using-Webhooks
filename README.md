# Triggering-jenkins-job-using-Webhooks
Triggering jenkins job using webhooks
Launch an instance
Select amazon linux instance and configure the settings. In the step-5 Add tags with the name and in the step-6 configure security group with a new security group(open 8080 port for jenkins with custom TCP in the inbound rule with CIDR add below IP adress along with my IP to access from anywhere for now).https://api.github.com/meta

"192.30.252.0/22",

 "185.199.108.0/22",
 
 "140.82.112.0/20" 
A key pair gets downloaded before launching the instance

Change the read only permissions to the pem file
chmod 400 Work.pem

Connect to the instance from the command line
ssh -i "<path of the pem file>" ec2-user@ec2-36-215-39-147.us-west-2.compute.amazonaws.com

Update all the applications before installing
sudo yum update -y

Install java
https://bhargavamin.com/how-to-do/setting-up-java-environment-variable-on-ec2/

sudo yum install java-1.8.0
sudo yum remove java-1.7.0-openjdk
To check the java installed location
file $(which java)

To set the java path
sudo /usr/sbin/alternatives --set java /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/java
sudo /usr/sbin/alternatives --set javac /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/javac
export JAVA_HOME="/usr/lib/jvm/jre-1.8.0-openjdk.x86_64"
export PATH=$JAVA_HOME/bin:$PATH  
To set the java path to bash_profile
add below content to ~/.bash_profile file

export JAVA_HOME="/usr/lib/jvm/jre-1.8.0-openjdk.x86_64"
export PATH=$JAVA_HOME/bin:$PATH
To check JAVA_HOME
echo $JAVA_HOME

Install Maven
http://www.aodba.com/how-to-install-apache-maven-on-amazon-linux/

sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
sudo yum install -y apache-maven
mvn --version
Install Git
Install Git using yum
sudo yum install git -y
git version
Installation of Jenkins
https://medium.com/@itsmattburgess/installing-jenkins-on-amazon-linux-16aaa02c369c

sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
sudo service jenkins start
To access Jenkins server
Make sure port 8080 is open in security group inbound rules of that virtual machine. http://{ec2-public-dns}:8080

Jenkins Home directory
/var/lib/jenkins/

Token generation
Go to github account settings -> Developer settings ->personal access tokens Here you can generate a new token with complete access. copy the token paste it in jenkins credentials

Access jenkins using public Ip:port number of jenkins
Go to manage jenkins-> configure system scorll down to github and add a new github server

Add jenkins credentials with username and password(token generated)
Configure Webhooks
Go to application repository in github Repository settings -> Webhooks ->Add a new Webhook -> give payload URL as (public ip:8080)jenkins url/github-webhook/

Select content type as application/json -> add webhook

Create jenkins job
New item -> in SCM select git -> give repository url ->
Add same credentials which we have given in configure system near github server

In build triggers select Github Hook trigger for GITScm polling give build commands in execute shell save the jenins job

Whenever the changes are made to any file in that git repository it will automatically triggers the build job in jenkins using this webhooks concept.
