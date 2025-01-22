pipeline {
    agent {
        docker {
            image 'docker:stable'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    environment {
        IMAGE_NAME = 'sanjayraj/appv1'  // Change as needed
        DOCKER_CONFIG = '/tmp/.docker' // Docker config directory
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
        stage('Deploy Locally') {
            steps {
                script {
                    echo 'Deploying Docker container locally...'
                    sh '''
                        docker pull $IMAGE_NAME
                        docker stop app || true
                        docker rm app || true
                        docker run -d --name app -p 9000:9000 $IMAGE_NAME
                    '''
                }
            }
        }
    }
    post {
        always {
            script {
                echo 'Logging out from Docker Hub...'
                sh 'docker logout'
            }
        }
        success {
            echo 'Pipeline executed successfully! Image pushed to Docker Hub and deployed locally.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
