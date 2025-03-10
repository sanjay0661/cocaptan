pipeline {
    agent any
    environment {
        DEV_SERVER = "ubuntu@dev-server-ip"
        PROD_SERVER = "ubuntu@prod-server-ip"
        RELEASE_SERVER = "ubuntu@release-server-ip"
        SSH_KEY = credentials('ssh-key-id')  // Store SSH key in Jenkins credentials
    }
    stages {
        stage('Check Branch') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'development') {
                        deployToServer(DEV_SERVER)
                    } else if (env.BRANCH_NAME == 'release') {
                        deployToServer(RELEASE_SERVER)
                    } else if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
                        deployToServer(PROD_SERVER)
                    } else {
                        echo "No deployment needed for branch: ${env.BRANCH_NAME}"
                        currentBuild.result = 'ABORTED'
                        return
                    }
                }
            }
        }
    }
}

def deployToServer(targetServer) {
    stage("Deploy to ${targetServer}") {
        steps {
            echo "Deploying to ${targetServer}..."
            sshagent(['ssh-key-id']) {
                sh """
                ssh -o StrictHostKeyChecking=no ${targetServer} '
                cd /path/to/app &&
                git pull origin ${env.BRANCH_NAME} &&
                docker build -t my-app:${env.BRANCH_NAME} . &&
                docker run -d --name my-app-${env.BRANCH_NAME} -p 80:80 my-app:${env.BRANCH_NAME}
                '
                """
            }
        }
    }
}
