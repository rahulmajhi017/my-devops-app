pipeline {
    agent any

    environment {
        IMAGE = "rahuljr08/my-devops-app:latest"
        DOCKER_USER = "rahuljr08"
        KUBE_NAMESPACE = "default"
        DEPLOYMENT = "my-web-app"
        CONTAINER = "my-web-app"
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

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-token', variable: 'DOCKER_TOKEN')]) {
                    sh '''
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
                sh '''
                    echo "Checking deployment..."
                    kubectl get deployment $DEPLOYMENT -n $KUBE_NAMESPACE

                    echo "Updating image..."
                    kubectl set image deployment/$DEPLOYMENT \
                        $CONTAINER=$IMAGE \
                        -n $KUBE_NAMESPACE

                    echo "Waiting for rollout..."
                    kubectl rollout status deployment/$DEPLOYMENT -n $KUBE_NAMESPACE
                '''
            }
        }
    }
}
