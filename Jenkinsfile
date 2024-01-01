pipeline {
    agent { label "local" }
    environment {
    DOCKERHUB_CREDENTIALS = credentials('docker-hub-sankar')
    }
    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/sankar0812/jenkins-kubernetes-example.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Install Buildx
                  sh 'docker buildx build -t sankar0812/nodejsapp-1.0:latest .'
            }
        }
        stage('Push Docker Image') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'docker-hub-sankar', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    // Your Docker commands here
                    sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'docker push sankar0812/nodejsapp-1.0:latest'
                 }  
            }
        }
        stage('Deploy to Kubernets'){
            steps{
                script{
                    dir('Kubernetes') {
                      withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                      sh 'kubectl apply -f deployment.yml'
                      sh 'kubectl apply -f service.yml'
                      }   
                    }
                }
            }
        }
    }
}
