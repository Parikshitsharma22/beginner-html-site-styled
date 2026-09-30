pipeline {
    agent { label 'kubernetes' }

    environment {
        IMAGE_NAME = 'parikshitsharma22/beginner-website'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f deployment.yaml
                    kubectl apply -f service.yaml
                    kubectl set image deployment/beginner-website beginner-website=$IMAGE_NAME:$BUILD_NUMBER
                    kubectl rollout status deployment/beginner-website
                    kubectl get pods
                    kubectl get svc
                '''
            }
        }
    }
}
