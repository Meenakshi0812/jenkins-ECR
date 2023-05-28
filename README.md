## jenkins pipeline to create docker image and upload to AWS ECR 
- Jenkins pipeline will do
- automate builds
- automate docker image creation
- automate docker image upload into AWS ECR
- automate docker container provisioning

# Pre-requisites:
- Jenkins is up and running
- Docker is installed in Jenkins machine.
- Docker plug-in installed in Jenkins.
- Docker pipeline plug-in installed in Jenkins.
- Repo created in ECR.
- Make sure port 8096 is opened up in firewall rules. 
- Create an IAM role with AmazonEC2ContainerRegistryFullAccess policy, attach role to Jenkins EC2 instance
select the EC2 instance in which jenkins is created-> actions->security->modify IAM rule-> create new IAM rule->search for AmazonEC2FullAcess->done
-  Make sure AWScli is installed in Jenkins instance.
 
now, integrate jenkins with docker
Now Login to Jenkins EC2 instance, execute below commands:

#Add jenkins user to Docker group
- sudo usermod -a -G docker jenkins

#Restart Jenkins service
- sudo service jenkins restart

#Reload system daemon files
- sudo systemctl daemon-reload

#Restart Docker service as well

- sudo service docker stop
- sudo service docker start

- Build the jenkinsfile.once build got success you can able to access it to using dns:8096.
