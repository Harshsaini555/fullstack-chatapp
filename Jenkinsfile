pipeline {
    agent any

    stages {
        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t chatapp-backend:latest ./backend
                docker build -t chatapp-frontend:latest ./frontend
                '''
            }
        }
        
        stage('Push Docker Images') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker image tag chatapp-backend:latest harshsaini05/chatapp-backend:latest
                    docker image tag chatapp-frontend:latest harshsaini05/chatapp-frontend:latest
        
                    docker push harshsaini05/chatapp-backend:latest
                    docker push harshsaini05/chatapp-frontend:latest
                    '''
                }
            }
        }
        stage('Debug K8s') {
            steps {
                sh 'kubectl cluster-info'
                sh 'kubectl get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/namespace.yml
                
                kubectl apply -f k8s/mongodb_Pv.yml
                kubectl apply -f k8s/mongodb-pvc.yml
                kubectl apply -f k8s/mongodb-service.yml
                kubectl apply -f k8s/mongodb-deployment.yml

                kubectl apply -f k8s/frontend-service.yml
                kubectl apply -f k8s/frontend-deployment.yml
                
                kubectl apply -f k8s/backend-secrets.yml
                kubectl apply -f k8s/backend-service.yml
                kubectl apply -f k8s/backend-deployment.yml
        
                kubectl apply -f k8s/ingress.yml
                '''
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
