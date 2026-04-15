pipeline {
    agent any

    environment {
        IMAGE = "rahuljr08/my-devops-app:latest"
        DOCKER_USER = "rahuljr08"
    }

    stages {

        stage('Test Tools') {
            steps {
                sh 'docker --version'
                sh 'git --version'
                sh 'kubectl version --client || true'
            }
        }

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

        stage('Docker Login (FIXED)') {
            steps {
                withCredentials([string(credentialsId: 'docker-token', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                        docker logout || true
                        echo "$DOCKER_TOKEN" | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE'
            }
        }

        stage('Deploy to k3s') {
    steps {
        sh 'kubectl set image deployment/my-web-app my-web-app=rahuljr08/my-devops-app:latest'
             }
         } 
    }
}
