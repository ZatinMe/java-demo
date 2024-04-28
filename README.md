Test project for CI/CD pipeline and springboot learing
use this to install jenkins up and running -> https://medium.com/bb-tutorials-and-thoughts/how-to-run-jenkins-on-gcp-vm-29dc18490fae (use java-17-jdk)
take help from here -> https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos

    Start Jenkins
    You can enable the Jenkins service to start at boot with the command:
    
    sudo systemctl enable jenkins
    You can start the Jenkins service with the command:
    
    sudo systemctl start jenkins
    You can check the status of the Jenkins service using the command:
    
    sudo systemctl status jenkins

Once ready test curl http://localhost:8080 using ssh into the instance should return some html
use external IP from instance and use http://<ext_ip>:8080

Now install maven 
  Can follow this to install maven -> https://www.digitalocean.com/community/tutorials/install-maven-linux-ubuntu
  
Now install Git
  sudo apt-get install git-all
Now install Docker ( https://docs.docker.com/engine/install/ubuntu/ )

    sudo apt-get install curl apt-transport-https ca-certificates software-properties-common
    sudo apt-get remove docker docker-engine docker.io
    sudo apt-get update
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo apt-key fingerprint 0EBFCD88
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    xenial \
    stable"
    sudo apt-get update
    sudo apt-get install docker-ce
    sudo docker run hello-world

Now installation are Done.... Make a jenkins job and try
    
   
