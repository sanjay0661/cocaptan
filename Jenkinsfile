pipeline {
    agent any
    stages {
        stage('Docker Access Test') {
            steps {
                script {
                    sh 'docker version'
                }
            }
        }
    }
    post {
        always {
            script {
                echo "Pipeline completed!"
            }
        }
    }
}
