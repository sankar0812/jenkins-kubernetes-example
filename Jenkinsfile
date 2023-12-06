pipeline{
    agent any
    stages {
        stage('SCM Checkout') {
            steps{
              git "https://github.com/sankar0812/jenkins-kubernetes-example.git" 
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
                 withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u devopshint -p ${dockerhubpwd}'
                 }  
                 sh 'docker push devops/nodejsapp-1.0:latest'
                }
            }
        }
    
        stage('Deploy App on k8s') {
            steps {
               sshagent(['k8s']) {
               sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml ubuntu@IPofk8scluster:/home/ubuntu"
               script {
                  try{
                      sh "ssh ubuntu@IPofk8scluster kubectl create -f ."
                  }catch(error){
                      sh "ssh ubuntu@IPofk8scluster kubectl create -f ."
                   }
               }
               }
      
            }
        }
    }
}
