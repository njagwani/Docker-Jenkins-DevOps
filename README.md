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

Once the image has been downloaded, run the below command to check find where is the image located.

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


Please note that you can use "docker-compose stop" to stop the Jenkins container and docker-compose start" to start the Jenkins Container. Also you can use "docker-compose restart jenkins" to restart the jenkins container service.

In case if you want to completely delete your entire service then enter the command "docker-compose down"

## STAGE 2

- Creating Jenkins Job
- Redirecting Jenkins Job Output
- Executing a bash script from Jenkins
- Adding Parameters to your Jenkins Job
- Creating Jenkins List Parameter with help of script
- Adding basic logic and boolean parameters to Jenkins Job. 

Lets navigate to Jenkins and create a job.

Lets click on create new item. 

![](/Images/Image24.PNG)

We are not configuring a job or a task but the concept in Jenkins is a job.

Navigate to Build Environment Tab and select "Execute Shell" under Build

![](/Images/Image25.PNG)

One very important thing we need to know here is that everything that is going to happen under the Jenkins Job will occur within the container. 

When we say within the container, it means that we need to go inside the container by running the below command.This is just for understanding

$ docker exec -ti jenkins bash (jenkins is the name of my container)

![](/Images/Image26.PNG)

Lets go back to your browser and under Execute shell enter "echo Hello World" and click on Save. 

![](/Images/Image27.PNG)

Click on Build Now

![](/Images/Image28.PNG)

In the console output, you can see that the build was successful priniting "Hello World".

![](/Images/Image29.PNG)

Now, lets try to go insider the container by entering the command "docker exec -ti jenkins bash" and lets run a command that would echo the date and time.

$ echo The current date is $(date)

![](/Images/Image31.PNG)

You should immediately get the current date and time. 

Similarly, a job can be executed under Build shell by copying this command "echo The current date is $(date)" under Build Environment Execute Shell and pasting it here. 

![](/Images/Image32.PNG)

Lets go ahead and modify our Execute Shell on Jenkins Broswer to be as below where we end up resurrecting to a file under a container

![](/Images/Image33.PNG)

Select Build to build this task, you will see that task was built successfully. 

![](/Images/Image34.PNG) 

Verify the output under the path /tmp/info in your container. You should be able to see the output for the job that was executed under your last build. 

![](/Images/Image35.PNG) 

Execute a script from Jenkins

In order to get started, let us first exit out of the container. Lets create a script by the name script.sh that will output "Hello, Your Firstname and Lastname"

![](/Images/Image36.PNG)

Make sure you give executable permissions to run the script and lets go ahead and run the script "script.sh"

$ ./script.sh Nitin Jagwani

![](/Images/Image37.PNG)

Now in order to copy to script from my VM to my docker container, it is done in the following way

$ docker cp script.sh jenkins:/tmp/script.sh

Now go inside your container and cat /tmp/script.sh to ensure that script.sh file is present in your conatiner. You should be able to see the contents of script.sh file. 

![](/Images/Image38.PNG)

We can build a job by pasting the above script under Build Environment Execute Shell in Jenkins.

![](/Images/Image39.PNG)

![](/Images/Image40.PNG)

Adding Paramenters to Job in Jenkins to dynamically be able to set the name that we want to read

Go to your main project and select "Configure"

![](/Images/Image41.PNG)

Under "General" tab, select the option "the project is parameterized" and in the "Add Paramter" dropdown select the option "String Parameter"

![](/Images/Image42.PNG)

I have entered FIRST_NAME for Name and gave the default value has Nitin. I created a 2nd string parameter, entered "SECOND_NAME" for name and Jagwani for default value. 

![](/Images/Image43.PNG)

I edited my shell and added the parameters.

echo "Hello, $FIRST_NAME $SECOND_NAME"

![](/Images/Image44.PNG)

Save your build and now you should see that build option has now changed to "Build with Parameters"

![](/Images/Image45.PNG)

I decided to change to FIRST_NAME to Jones and SECOND_NAME to Jackson and ran the build

![](/Images/Image46.PNG)

And My Build was successful!!!

![](/Images/Image47.PNG)

Now lets go to configure and create a third parameter but this time select "Choice parameter", give the name to be LAST_NAME and Default Value to be any 3 names

![](/Images/Image48.PNG)

Edit your LAST_NAME to your execute shell and click on Save.

![](/Images/Image49.PNG)


Select the option "Build with Paramter", you should then be able to see that you will get to pick from from any of the 3 names for LAST_NAME. Click on Build

![](/Images/Image50.PNG)


In the below image, you can see that my build ran successfully without any errors.

![](/Images/Image51.PNG)

Now the last stage is on how to add basic logic and boolean parameters. For this stage I decided to modify my script as below. 

![](/Images/Image52.PNG)

Now, go ahead and copy your script to your container by running the command "docker cp script.sh jenkins:/tmp/script.sh"

![](/Images/Image53.PNG)

Now lets go to Jenkins project, edit the parameters by removing the SECOND_NAME and add another paramter by Selecting "Boolean". Enter "SHOW" under name and check the box for "Set by default"

![](/Images/Image54.PNG)

Under execute shell, paste the path of script file in container followed by $FIRST_NAME $LAST_NAME $SHOW and then click on Save.

![](/Images/Image55.PNG)

Now, Select the option "Build with Parameters", uncheck the "SHOW" option which consider this as a false entry and run the Build. 

![](/Images/Image56.PNG)

You will get a message "if you want to see the name, please checkbox the show option"

![](/Images/Image57.PNG)

Now lets build a job again, but this time lets checkbox the "show" option

![](/Images/Image58.PNG)

In the console output, you can see that checking the show option box, accepts it as true and we got the result "Hello, Nitin Sandy".

![](/Images/Image59.PNG)






























