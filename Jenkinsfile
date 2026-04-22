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

        stage('Manual Approval') {
            steps {
                emailext(
                    subject: "Approval Needed: ${JOB_NAME} #${BUILD_NUMBER}",
                    body: """
Hi Team,

Build #${BUILD_NUMBER} is waiting for approval.

Job Name : ${JOB_NAME}
Build URL: ${BUILD_URL}

Please login to Jenkins and approve the build to continue.
""",
                    to: "shanchalsenthil@gmail.com"
                )

                input message: "Approve to continue deployment?",
                      submitter: "admin",
                      ok: "Approve"
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
