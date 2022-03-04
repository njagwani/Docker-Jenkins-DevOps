# Docker-Jenkins-DevOps

Hello and Welcome to my Docker Jenkins DevOps Project where I will be integrating Jenkins with Docker, Ansible, AWS, GI, Email, Maven and more!
I will also be using the best practices of Continuous Integration and Continuos Depployment which is highly essential in todays world of DevOps keeping in mind that Automation is the key. 

## STAGE 1 
- Installing Docker on CentOS7
- Installing Docker Compose
- Installing Jenkins using Docker

Firstly, let start off by Installing Docker on Cent OS 7 

#### Installing Docker on Cent OS 7

Screenshot showcading that I am able to access my CentOS 7 VM

![](/Images/Image1.png)

Kindly refer to below link for installing Docker on CentOS (https://docs.docker.com/engine/install/centos/) or follow along:

Set up the repository
Install the yum-utils package (which provides the yum-config-manager utility) and set up the stable repository.
$ sudo yum install -y yum-utils

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

Install Docker Engine
Install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:
$ sudo yum install docker-ce docker-ce-cli containerd.

Start Docker
 $ sudo systemctl start docker

Verify that Docker Engine is installed correctly by running the hello-world image.
 $ sudo docker run hello-world

![](/Images/Image2.png)

Enable Docker service at boot

$ systemctl enable docker

Now when you run docker ps, you will get an error as seen below 

$ docker ps

Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/json": dial unix /var/run/docker.sock: connect: permission denied

Please add the user to the group docker by entering the command below, please note that nitin is my user. 
sudo usermod -aG docker nitin 

Logout out of your current session and then log back in. 

You should now be able to run the docker ps command without any issues

![](/Images/Image3.png)
#### Installing Docker Compose 
Docker Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services.

$ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

Apply executable permissions to the binary:

$ sudo chmod +x /usr/local/bin/docker-compose

Test the installation

$ docker-compose --version

![](/Images/Image4.png)
#### Installing Jenkins using Docker

Download the Jenkins Docker Image 

The official image of jenkins on Docker can be found here https://hub.docker.com/_/jenkins/

Run this below mentioned command to install Jenkins

$ docker pull jenkins/jenkins

![](/Images/Image5.PNG)

Once the image has been downloaded, run the below command to check find how where is the image located.

$ docker images

![](/Images/Image6.PNG)










