pipeline{
    agent {label "dev"}
    environment{
     DOCKERHUB_CREDENTIALS = credentials('docker-hub-sankar')
    }
    stages {
        stage('SCM Checkout') {
            steps{
              git 'https://github.com/sankar0812/jenkins-kubernetes-example.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t devops/nodejsapp-1.0:latest .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                 sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                 sh 'docker push devops/nodejsapp-1.0:latest'
                 }  
            }
        }
    
        stage('Deploy App on k8s') {
            steps {
               sshagent(['k8s']) {
               sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml goveadmin@192.168.29.140:/home/ubuntu"
               script {
                  try{
                      sh "ssh goveadmin@192.168.29.140 kubectl create -f ."
                  }catch(error){
                      sh "goveadmin@192.168.29.140 kubectl create -f ."
                   }
               }
               }
      
            }
        }
    }
    post{
    always{
       sh 'docker logout'
    }
  }
}
