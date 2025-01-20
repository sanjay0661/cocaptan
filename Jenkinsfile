pipeline {
    agent {
        docker {
            image 'docker:stable'
            args '-v /var/run/docker.sock:/var/run/docker.sock'  // Docker socket access
        }
    }
    environment {
        DOCKER_USERNAME = 'sanjayraj'  // Hardcoded Docker Hub username
        DOCKER_PASSWORD = 'dckr_pat_N7EQOyJR1wE2vdbbZjYOfV0HAAc'  // Hardcoded Docker Hub password/token
        IMAGE_NAME = 'sanjayraj/appv1'  // Replace with your Docker Hub image name
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the code from the repository
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t $IMAGE_NAME .'  // Build Docker image
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    echo 'Logging into Docker Hub...'
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''  // Log in to Docker Hub using the credentials
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh 'docker push $IMAGE_NAME'  // Push Docker image to Docker Hub
                }
            }
        }
    }
    post {
        always {
            script {
                echo 'Logging out from Docker Hub...'
                sh 'docker logout'  // Always log out from Docker Hub after completion
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
