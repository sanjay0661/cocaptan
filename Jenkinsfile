pipeline {
    agent {
        docker {
            image 'docker:stable'
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Access host Docker
        }
    }
    environment {
        DOCKER_USERNAME = credentials('DOCKER_HUB_CREDENTIALS_USR')
        DOCKER_PASSWORD = credentials('DOCKER_HUB_CREDENTIALS_PSW')
        IMAGE_NAME = 'sanjayraj/appv1' // Replace with your Docker Hub image repository
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Clones the GitHub repository containing Dockerfile
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building the Docker image...'
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    echo 'Logging into Docker Hub...'
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                    '''
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
    }
    post {
        always {
            script {
                echo 'Logging out from Docker Hub...'
                sh 'docker logout'
            }
        }
        success {
            echo 'Pipeline executed successfully! Image pushed to Docker Hub.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
