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
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 691708062387.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 691708062387.dkr.ecr.us-east-1.amazonaws.com/my-new-repo:latest'
         }
        }
      }
   
         // Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
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
