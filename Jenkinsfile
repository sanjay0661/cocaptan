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
        KUBECONFIG = '/var/lib/jenkins/.kube/config'  // Add Kube config path for Jenkins
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
                    echo 'Setting up kubectl and deploying to Minikube...'
                    // Ensure kubectl is properly configured with Minikube context
                    sh '''
                        echo "Setting Minikube context..."
                        sudo minikube update-context
                        sudo minikube start
                        sudo kubectl config use-context minikube
                        sudo kubectl apply -f deployment.yaml
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
            echo 'Pipeline executed successfully! Image pushed to Docker Hub and deployed to Minikube.'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
