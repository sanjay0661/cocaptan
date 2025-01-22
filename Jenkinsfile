pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'sanjayraj/appv1'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Push Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                script {
                    echo 'Deploying to Minikube...'
                    sh '''
                        kubectl apply -f deployment.yml
                        kubectl rollout status deployment/my-app-deployment
                    '''
                }
            }
        }
    }
    post {
        always {
            script {
                echo 'Cleaning up temporary data...'
                sh 'docker logout'
            }
        }
        success {
            echo 'Deployed successfully to Minikube.'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
