pipeline {
    agent any

    environment {
        IMAGE = "rahuljr08/my-web-app:latest"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/rahulmajhi017/my-devops-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-cred', url: '']) {
                    sh 'docker push $IMAGE'
                }
            }
        }

        stage('Deploy to k3s') {
            steps {
                sh 'kubectl set image deployment/my-web-app my-web-app=$IMAGE'
                sh 'kubectl rollout status deployment/my-web-app'
            }
        }
    }
}
