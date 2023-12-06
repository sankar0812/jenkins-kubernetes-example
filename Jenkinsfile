pipeline{
    agent {label "dev"}
    environment{
     DOCKERHUB_CREDENTIALS = credentials('docker-hub-sankar')
    }
    stages {
    //     stage('SCM Checkout') {
    //         steps{
    //           git 'https://github.com/sankar0812/jenkins-kubernetes-example.git'
    //         }
    //     }
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
                 withCredentials([usernamePassword(credentialsId: 'docker-hub-sankar', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    // Your Docker commands here
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
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
