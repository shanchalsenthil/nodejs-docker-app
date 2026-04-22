pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'node-app', description: 'Docker image name')
        string(name: 'PORT', defaultValue: '9000', description: 'Port number')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/shanchalsenthil/nodejs-docker-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${params.IMAGE_NAME} ."
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop node-container || true
                docker rm node-container || true
                docker run -d -p ${params.PORT}:3000 --name node-container ${params.IMAGE_NAME}
                """
            }
        }
    }
}
