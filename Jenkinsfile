pipeline {
    agent any

    stages {
        stage('Deploy') {
            steps {
                script {
                    sh 'docker-compose up -d --build'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh '''
                        sleep 30
                        curl -f http://localhost:5001/health
                        curl -f http://localhost:8081 || exit 1
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment and tests completed successfully!'
        }
        failure {
            echo 'Deployment or tests failed.'
        }
    }
}
