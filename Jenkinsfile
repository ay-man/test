pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'bf46b830-94c4-474d-b476-c240a2aeb21c'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build and Test') {
            steps {
                sh 'docker build -t my_fastapi_app:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_HUB_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login --username $DOCKER_USERNAME --password-stdin
                        docker tag my_fastapi_app:latest $DOCKER_USERNAME/my_fastapi_app:latest
                        docker push $DOCKER_USERNAME/my_fastapi_app:latest
                    '''
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['65365f6e-5b9a-4941-9794-01f35588600e']) {
                    sh '''
                        ssh jenkins@10.0.2.9 "docker pull aym4n/my_fastapi_app:latest && docker stop my_fastapi_app || true && docker rm my_fastapi_app || true && docker run --name my_fastapi_app -d -p 8000:80 aym4n/my_fastapi_app:latest"
                    '''
                }
            }
        }
    }
}
