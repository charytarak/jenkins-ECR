pipeline {
    agent any
     environment {
        registry = "341339702394.dkr.ecr.us-east-1.amazonaws.com/demo-ecr-repo"
    }
   
    stages {
          stage('Checkout') {
            steps {
                git branch: 'main', credentialsId: '5025d40b-a1f0-4039-92ca-d08dac036ded', url: 'https://github.com/charytarak/jenkins-ECR.git'
            }
        }
           stage('Building image') {
             steps{
                  script {
                   dockerImage = docker.build registry
                   }
      }
           }
    
            stage('Pushing to ECR') {
             steps{  
                  script {
               withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws_cred', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 341339702394.dkr.ecr.us-east-1.amazonaws.com'
     sh 'docker push 341339702394.dkr.ecr.us-east-1.amazonaws.com/demo-ecr-repo:latest'
}

}
                  }
            }
             stage('stop previous containers') {
               steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
         }
       }
            stage('Docker Run') {
              steps{
                   script {
                sh 'docker run -d -p 8096:5000 --rm --name mypythonContainer 341339702394.dkr.ecr.us-east-1.amazonaws.com/demo-ecr-repo:latest'     
      }
    }
        }
    }
  }
