pipeline {
    agent any
    environment {
        // For Docker Hub
        DOCKER_HUB_CREDENTIALS = 'bf46b830-94c4-474d-b476-c240a2aeb21c'
    }
    stages {
        stage('Checkout') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github_credentials', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_PASSWORD')]) {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [],
                        userRemoteConfigs: [[url: 'https://$GITHUB_USERNAME:$GITHUB_PASSWORD@github.com/test.git']]
                    ])
                }
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
                sshagent(credentials: ['65365f6e-5b9a-4941-9794-01f35588600e']) {
                    sh '''
                        ssh jenkins@10.0.2.9 "docker pull aym4n/my_fastapi_app:latest && docker stop my_fastapi_app || true && docker rm my_fastapi_app || true && docker run --name my_fastapi_app -d -p 8000:80 aym4n/my_fastapi_app:latest"
                    '''
                }
            }
        }
    }
}

