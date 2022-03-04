# Docker-Jenkins-DevOps

Hello and Welcome to my Docker Jenkins DevOps Project where I will be integrating Jenkins with Docker, Ansible, AWS, GI, Email, Maven and more!
I will also be using the best practices of Continuous Integration and Continuos Depployment which is highly essential in todays world of DevOps keeping in mind that Automation is the key. 

## STAGE 1 
- Installing Docker on CentOS7
- Installing Docker Compose
- Installing Jenkins using Docker

Firstly, let start off by Installing Docker on Cent OS 7 

#### Installing Docker on Cent OS 7

Screenshot showcasing that I am able to access my CentOS 7 VM

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

$ sudo usermod -aG docker nitin 

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

To know where your docker is saving the files, here is the command

$ docker info | grep -i root

Output >> Docker Root Dir: /var/lib/docker

And to know, how much space is my docker taking, then here it is

$ sudo du -sh /var/lib/Docker

Let go ahead and create a directory 

$ mkdir jenkins-data

This jenkins directory should ideally be under /home/nitin

![](/Images/Image7.PNG)

Lets create a sample docker compose yaml file under jenkins-data

$ cd jenkins-data

Lets create a jenkins_home directory to map it to our volume otherwise data within the container will be destroyed if in case I dont save that data into a volume

$ mkdir jenkins_home

Assuming that you are under your path /home/nitin/jenkins-data, you should see jenkins_home. Lets create a sample docker compose yaml file.

$ vi docker-compose.yml

Docker compose file like in the script is a definition where I have defined which services we want to spin.

In our docker compose file, I have defined a service called as jenkins. The container name is jenkins and the image is jenkins/jenkins which is exposed to port 8080. It is going to save all the information under /jenkins/home that was created earlier. It is then going to bind the service to the network called net. 

![](/Images/Image8.PNG)

Whats next??

I am going to spin up Jenkins by starting our Container service using Docker

But before that we need to make user that user nitin, id = 1000 can write to jenkins_home folder

![](/Images/Image9.PNG)

$ sudo chown 1000:1000 jenkins_home -R

![](/Images/Image10.PNG)

Now, that we gave the persmissions, we need to spin up the server using the Docker Compose command as below:

$ docker-compose up -d

![](/Images/Image11.PNG)

You can now see you jenkins server running here by running the docker ps command.

$ docker ps

![](/Images/Image12.PNG)

In order to check the logs of your Jenkins container, then you can the run command docker logs -f "container name". Jenkins is my container as seen below. 

$ docker log -f jenkins

![](/Images/Image13.PNG)

Copy the IP Address of your machine which can be obtained by using the command "ip a", paste the IP in the web browser exposed over port 8080. You should then be able to see a jenkins page similar to below.

![](/Images/Image14.PNG)

Password can be found by running the command docker logs -f "container name"

$ docker logs -f jenkins 

![](/Images/Image15.PNG)

Copy the password and paste the it on your web browser and hit on "Continue". Then Select the option "Install suggested plugins"

![](/Images/Image16.PNG)

![](/Images/Image17.PNG)

Once the suggested plugins have been installed, then go ahead and create First Admin User. Once you have created the admin user, you should then able to login to your Jenkins Dashboard successfully.

![](/Images/Image18.PNG)

![](/Images/Image19.PNG)

![](/Images/Image20.PNG)

![](/Images/Image21.PNG)

We are going to start using a local DNS for Jenkins Server. 

Navigate to path on your local Machine C:\Windows\System32\drivers\etc and open hosts file with Administartor rights. Copy the IP address from your web broswers and pasted it into you hosts file with dns record.

![](/Images/Image22.PNG)

You should not be able to use your local DNS (jenkins.local:8080) to bring up the Jenkins page. 

![](/Images/Image23.PNG)

























