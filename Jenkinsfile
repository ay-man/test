pipeline {
    agent any 
    environment {
        DOCKER_HUB_CREDENTIALS = 'bf46b830-94c4-474d-b476-c240a2aeb21c'
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
                withCredentials([string(credentialsId: env.DOCKER_HUB_CREDENTIALS, variable: 'DOCKER_HUB_PASS')]) {
                    sh '''
                        echo "$DOCKER_HUB_PASS" | docker login --username aym4n --password-stdin
                        docker tag my_fastapi_app:latest aym4n/my_fastapi_app:latest
                        docker push aym4n/my_fastapi_app:latest
                    '''
                }
            }
        }
        stage('Deploy to Server') {
            steps {
                sshagent(credentials: ['id_rsa']) {
                    sh '''
                        ssh jenkins@10.0.2.9 "docker pull aym4n/my_fastapi_app:latest && docker stop my_fastapi_app || true && docker rm my_fastapi_app || true && docker run --name my_fastapi_app -d -p 8000:80 aym4n/my_fastapi_app:latest"
                    '''
                }
            }
        }
    }
}
