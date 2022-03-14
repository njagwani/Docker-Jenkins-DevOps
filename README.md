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

Now, that I gave the persmissions, Lets spin up the server using the Docker Compose command as below:

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

I am going to start using a local DNS for Jenkins Server. 

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
- Adding basic logic and boolean parameters to Jenkins Job

Lets navigate to Jenkins and create a job.

Lets click on create new item. 

![](/Images/Image24.PNG)

I am not configuring a job or a task but the concept in Jenkins is a job.

Navigate to Build Environment Tab and select "Execute Shell" under Build

![](/Images/Image25.PNG)

One very important thing we need to know here is that everything that is going to happen under the Jenkins Job will occur within the container. 

When we say within the container, it means that we need to go inside the container by running the below command. This is just for understanding

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

Lets go ahead and modify our Execute Shell on Jenkins Broswer to be as below where I end up redirecting to a file under a container

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

In the console output, you can see that checking the show option box, accepts it as true and got the result "Hello, Nitin Sandy".

![](/Images/Image59.PNG)

## STAGE 3

- Excuting Jenkins jobs in remote machines
- Installing Jenkins Plugin (SSH Plugin)
- Integrating Docker SSH server with Jenkins
- Running Jenkins Job on Docker container through SSH

Here I will be creating a new container which is going to have an SSH service so that I can connect from Jenkins container to this new container without the need for creating new VM.

Lets start off by creating a new directory as send below and then navigate to your newly created directory.

 ![](/Images/Image60.PNG)

On my Docker file, I am going to be adding the following:
- Pick CentOS as the base for operating system
- Install openssh server 
- Create a user for this distribution
- Assign a password for this user as standard input
- Create a home directory for this user, here I am creating .ssh folder to store some configurations so that I can connect to this container from Jenkins container. 
- Giving permissions to the newly created user to be able to write to .ssh folder

Now lets create a private ssh key to make connections in a secure way. 

$ ssh-keygen -f remote-key

 ![](/Images/Image61.PNG)

 Now this passphrase has created 2 files
  - remote-key (private certficate key)
  - remote-key.pub (public key)

 ![](/Images/Image62.PNG)


In order to make ssh connection to this container, we are going to open up our Docker file and copy this remote-key.pub to .ssh folder that we previously created.

The user that we created is the owner of everything that is under this directory /home/remote_user/.ssh/.

The remote user that we created and group remote user that us created by default is going to be the owner recursively of this folder.

So, whatever is going to be under .ssh folder is going to be owned by the user. Now we are going to give more permissions for these files that we created here. 

In order to run this server from scratch, ssh needs to create some global keys which can be done using the command "RUN /usr/sbin/ssh-keygen. This is needed for a docker container.

The last thing we need to do for our service to run is to solidify the CMD. We are instructing Docker on how the service should be started. 

 ![](/Images/Image63.PNG)

Now, lets go your docker-compose.yml file which is under jenkins-data, and lets modify our docker-compose file.

Its time to create another service called remote-host. Here we will set the container name, image, build and network. 

 ![](/Images/Image64.PNG)

Now in order to build our image, we need to execute the below command:

$ docker-compose build

 ![](/Images/Image65.PNG)

 After running the command docker images, you can see my remote-host image whcih has the necessary ssh service installed. 

 ![](/Images/Image66.PNG)

 Now lets go ahead and create a container using our newly created remote-host image by running the below commands

 $ docker-compose up -d

 ![](/Images/Image67.PNG)

 $ docker ps

 ![](/Images/Image68.PNG)

Lets go inside your jenkins container by entering the command " docker exec -ti jenkins bash" and here we can ssh to our newly create container named "remote-host"

$ docker exec -ti jenkins bash

$ ssh remote_user@remote_host

 ![](/Images/Image69.PNG)


Now lets copy the remote-key which can be found under centos7 directory to your jenkins container path /tmp/remote-key. You can use this key to connect to the remote_user@remote_host

$ docker cp remote-key jenkins:/tmp/remote-key

$ ssh -i remote-key remote_user@remote_host

The reason why we created a remote-key is to get authenticated without giving any password.

 ![](/Images/Image70.PNG)


Installing Plugins in Jenkins

Navigate to Jenkins Dashboard and Select "Manage Jenkins" and then click on "Manage Plugins"

 ![](/Images/Image75.PNG)

 Our goal is to install SSH plugin that I have already installed. 

  ![](/Images/Image76.PNG)

Now, that the SSH plugin is installed it is time to integrate Docker SSH server with Jenkins

Go to Jenkins Dashboard, Select "Manage Jenkins" and click "Manage Credentials"

  ![](/Images/Image71.PNG)

Go to your Jenkins user and then on the left side you will see an option "Add Credentials". Click on it. 

-  Select the Kind type : SSH Username with private key
-  Enter the username : remote_user
-  Select the option :Private Key" and paste your private key (Go to your path centos7 and enter the command "cat remote-key" to view the passphrase of your key, copy and paste it    here in the box provided.). Click on Ok.

  ![](/Images/Image72.PNG)

  ![](/Images/Image74.PNG)

Navigate to Jenkins Dashboard, Click on "Manage Jenkins" and then select "Configure System". Navigate to SS remote hosts, enter the Hostname of your remote container (remote_host), SSH port number is 22 and select "remote_user" from the credentials drop down. Click on "Check Connection" and you should be able to see that "connection was successfull".

  ![](/Images/Image77.PNG)

  ![](/Images/Image73.PNG)

Running a Jenkins job on Docker container via SSH

Navigate to your Jenkins Dashboard, Click on "New Item". Enter thne item name to be "remote-test", select the "Build Triggers" tab. Click on SSH site which should automatically be pre-populated. In the command shell, enter the following to build your job. Click on save. 

  ![](/Images/Image78.PNG)

Go ahead and Select "Build Now". In the console output, you can see that my build was successful. 

  ![](/Images/Image79.PNG)

Lets check this my going inside your remote-host container. You should be able to see the result from the Jenkins build. 

  ![](/Images/Image80.PNG)

## STAGE 4

- Creating MySQL server on Docker Container
- Creating a test database and creating tables on MySQL server installed on Docker Container 
- Creating an AWS S3 bucket and IAM access.
- Taking an SQK backup and uploading to S3 bucket manually 
- Automating the SQL Backup and Upload Process to AWS S3 Bucket with Shell Script 
- Managing AWS CLI and SQL DB credentials on Jenkins
- Creating a Jenkins Job to upload SQL DB backup to AWS S3 bucket with just one click. 

Create MySQL server on Docker container

You will need to go to your docker-compose.yml file located in jenkins-data directory and add the new service for your MySQL Server.

  ![](/Images/Image81.PNG)

In order to create your MySQL container, run the command "docker-compose up -d"

  ![](/Images/Image82.PNG)

  ![](/Images/Image83.PNG)

You can see that your service is up and running well by entering the command "docker ps"

  ![](/Images/Image84.PNG)

Now enter the command "docker logs -f db" to check if your MySQL service is ready for connections. I was able to see the message under my logs confirming that MySQL service is ready for connections. 

  ![](/Images/Image85.PNG)

Lets login to the new created container using the command "docker exec -ti db bash" and lets connect to our database in our container by entering the command "mysql -u root -p", enter the password and login should be successful. 

  ![](/Images/Image86.PNG)

Enter the command "show databases;" to veiw the information associated with your database. 

  ![](/Images/Image87.PNG)

Lets install MySQL Client and AWS CLI. We are going to be taking a backup from MySQL DB and upload it to AWS S3 Bucket. 

Navigate to your Dockerfile by first going to your centos7 directory and add the contents to install MySQL client and AWS CLI

  ![](/Images/Image88.PNG)

Lets go to your directory jenkins-data, and run the command docker-compose build which will install MySQL and AWS CLI.

  ![](/Images/Image89.PNG)

Image was build without any issues. 

  ![](/Images/Image90.PNG)

Now that MySQL has been installed on remote-host container, lets test it. Oops!!! I see an error messahe MySQL not found.

  ![](/Images/Image91.PNG)

Oh the reason why we got this message because I forgot to run "docker-compose up -d". Lets run this command. You can see that it has now re-created the remote-host container.

  ![](/Images/Image92.PNG)

Create MySQL database
Lets login to the remote-host container. Now that I am inside the container, I am going to login to MySQL container by entering the command "mysql -u root -h db_host -p". You will then be prompted to enter the pasword. Login in to should be successful.  

  ![](/Images/Image93.PNG)

Now that we are connected to our SQL server, lets create a new database. I am going to create a new database called testdb by entering the below command.

> create database testdb;

  ![](/Images/Image94.PNG)

  ![](/Images/Image95.PNG)

  ![](/Images/Image96.PNG)

  After creating a table testdb, I was then able to add information to my table using the below commands.

  ![](/Images/Image97.PNG)

  ![](/Images/Image98.PNG)


Lets create an AWS S3 bucket.

Navigate to AWS management console and search for S3, Select "Create Bucket". Give a bucket name, select the region and click on "Create"

  ![](/Images/Image99.PNG)

In the screenshot below, my AWS S3 bucket was successfully created in AWS Region eu-west-1

  ![](/Images/Image100.PNG)

Now, in order to upload our SQL backups to AWS S3 buckets, we need to provide authentication. In order to achieve this, let navigate to IAM service. Select 'Users' on the left select and click "Add users". 

  ![](/Images/Image101.PNG)

Enter the name of the user and select the option "Access key - Programmatic access", click on Next.

  ![](/Images/Image102.PNG)

Select the option "Attach existing policies", in the filter policy enter s3, Select the option AmazonS3FullAccess. Then click on Next and Create User

  ![](/Images/Image103.PNG)

You will then get to a page where you will be asked to Download .csv that containers your AccesskeyID and Secret access key. Go ahead and Download the csv file. 

  ![](/Images/Image104.PNG)

Lets take an SQL backup and upload to S3 bucket manually. We will first go inside our remote-host container and take the SQL backup by running the command "mysqldump -u root -h db_host -p testdb > /tmp/db.sql". Here were are redirecting it to /tmp/db.sql

  ![](/Images/Image105.PNG)

You can see that SQL backup was created successfully. 

  ![](/Images/Image106.PNG)

In order to meet the authentication requirement to connect to our S3 bucket, we need to export some environment variables. Paste the values from variables from the CSV file that you downloaded earlier.

  ![](/Images/Image107.PNG)

Run the below command to copy your MySQL backup to S3 bucket. You should then receive an upload message confirming your upload to S3 bucket. 

$ aws s3 cp /tmp/db.sql s3://mysqljenkins-backup/db.sql

  ![](/Images/Image108.PNG)

In the screengrab below, you can see that db.sql backup was successfully uploaded to S3 bucket. 

  ![](/Images/Image109.PNG)


Lets Automate the backup and upload process with a shell script

Go to your remote-host container, create a scipt in the tmp directory.

  ![](/Images/Image110.PNG)

Give executable permissions to your /tmp/script.sh by entering the command "chmod +x /tmp/script.sh". Lets run the script in the container by entering the command

$ /tmp/script.sh db_host 1234 test_db

  ![](/Images/Image111.PNG)

In the screenshot above, you can see that you I was able to getting different times for MySQL backup. 

Lets integrate the script with AWS CLI, by going to your script placed under /tmp/script.sh and lets define our AWS environment variables and add the command to copy the backup to s3 bucket.

  ![](/Images/Image112.PNG)

Since our DB password is exposed, its time to know to manage this information correctly by placing these passwords in the form of a secret. 

Lets navigate to Jenkins Dashboard, Click on Credentials and select Jenkins. 

  ![](/Images/Image113.png)

Select "Global Credentials"

  ![](/Images/Image114.png)

On the left side, select "Add Credentials". Select the kind to be "Secret text", Enter the SQL password in the Secret box. Under ID, please enter the name of the associated the password for example "MYSQL_PWD". Select 'Ok'

  ![](/Images/Image115.png)

In the screenshot, below you can see that global credential for MySQL_PWD was created successfully. Select the MYSQL_PWD and click on update.

  ![](/Images/Image116.png)

  ![](/Images/Image117.png)

Lets go ahead and do the same thing for storing AWS Credentials. Click on Add Credentials. Select the kind to be "Secret text", Enter the 'AWS_SECRET_KEY' password from the CSV file (previously downloaded), ID can be named as AWS_SECRET_KEY", click on Ok.

  ![](/Images/Image118.png)

  ![](/Images/Image119.png)

  ![](/Images/Image120.png)


Lets create a Jenkins Job to upload SQLDB backup to AWS.

On your Jenkins Dashboard, select "New Item", enter the name to be "aws-backup", select the project to be "Free Style". Now, click on Configure. 

  ![](/Images/Image120.PNG)

Under General, select the option 'This project is parameterized', Select the String Parameter as seen below and add the parameters.

  ![](/Images/Image121.PNG)

Add the secrets which were previously configured under Build Environment as seen below. Under Build, select the option "Execute shell script on remote host using ssh" and enter the command as seen below. Click on Save

  ![](/Images/Image122.PNG)

Let go ahead and select the option "Build with Parameters", enter the correct hostname, database name, AWS bucket name and then Select Build. 

  ![](/Images/Image123.PNG)

In the console output, we can see that build was successful and I was successfully able to upload SQL DB backup to AWS bucket with just one click. 

  ![](/Images/Image124.PNG)

Persist the script on remote host

Let go inside the remote-host container and copy the contents of the script,sh file located in /tmp/ directory

  ![](/Images/Image125.PNG)

Exit out of your container, be sure to be under jenkins-data directory and lets create a new file aws-s3.sh by entering the command "vi aws-s3.sh" and paste the contents here.

  ![](/Images/Image126.PNG)

One final change required is to add volume to the remote-host container so that whenever the container is deleted, the volume will be persisted. 

Go to your docker-compose.yml file and add the volume under remote-host container as seen below. Be sure with give the file aws-s3 executable permissions. 

  ![](/Images/Image127.PNG)



























 

















































































