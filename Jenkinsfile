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

        stage('Deploy Kubernetes (Apply YAMLs)') {
            steps {
                sh '''
                    echo "Applying Deployment & Service YAML..."

                    kubectl apply -f deployment.yaml -n $KUBE_NAMESPACE
                    kubectl apply -f service.yaml -n $KUBE_NAMESPACE

                    echo "Updating image..."
                    kubectl set image deployment/$DEPLOYMENT \
                        $CONTAINER=$IMAGE \
                        -n $KUBE_NAMESPACE

                    echo "Waiting for rollout..."
                    kubectl rollout status deployment/$DEPLOYMENT -n $KUBE_NAMESPACE
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    echo "Pods:"
                    kubectl get pods -n $KUBE_NAMESPACE

                    echo "Services:"
                    kubectl get svc -n $KUBE_NAMESPACE
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment SUCCESS"
        }
        failure {
            echo "❌ Deployment FAILED"
        }
    }
}
