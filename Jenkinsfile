pipeline {
    agent {
        docker {
            image 'docker:stable'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        IMAGE_NAME = 'sanjayraj/appv1'  
        DOCKER_CONFIG = '/tmp/.docker' 
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_hub_creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        echo 'Logging into Docker Hub...'
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        '''
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }
        stage('Deploy to Minikube') {
            steps {
                script {
                    echo 'Deploying to Minikube...'
                    sh '''
                        kubectl apply -f deployment.yml
                        kubectl rollout status deployment/app-deployment
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
            echo 'Pipeline executed successfully! Image pushed to Docker Hub and deployed to Minikube.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
