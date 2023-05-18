# jenkins-ECR
# ecs

- once docker file created
1. Build Docker Image
- docker build -t test .

2. Run container /w image
- docker run -d --publish 8888:5000 test

- to login ecr
- aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 691708062387.dkr.ecr.us-east-1.amazonaws.com
4. Tag the version
- docker tag test:latest 691708062387.dkr.ecr.us-east-1.amazonaws.com/test:latest
5. Upload
docker push 691708062387.dkr.ecr.us-east-1.amazonaws.com/test:latest

jenkins pipeline - ecs
requirements:
ecs plugin
manage nodes&clouds->configure clouds->add new cloud->amazon ec2 container service cloud



## jenkins pipeline to create docker image and upload to AWS ECR


 
Jenkins pipeline will do
automate builds
automate docker image creation
automate docker image upload into AWS ECR
automate docker container provisioning

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
sudo usermod -a -G docker jenkins

#Restart Jenkins service
sudo service jenkins restart

#Reload system daemon files
sudo systemctl daemon-reload

#Restart Docker service as well

sudo service docker stop
sudo service docker start

## Jenkinsfile:

pipeline {
    agent any
    environment {
        registry = "691708062387.dkr.ecr.us-east-1.amazonaws.com/my-new-repo"
    }
   
    stages {
          stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Meenakshi0812/jenkins-ECR.git'
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
     stage('Pushing to ECR') {
     steps{  
         script {
               withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws_cred', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 691708062387.dkr.ecr.us-east-1.amazonaws.com'
     sh 'docker push 691708062387.dkr.ecr.us-east-1.amazonaws.com/my-new-repo:latest'
}

}
      }
      
    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 8096:5000 --rm --name mypythonContainer 691708062387.dkr.ecr.us-east-1.amazonaws.com/my-new-repo:latest'
            }
      }
    }
    }
}
