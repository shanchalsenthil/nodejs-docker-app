pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: 'node-app', description: 'Docker image name')
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
                docker run -d -P --name node-container ${params.IMAGE_NAME}
                """
            }
        }

        stage('Show Running Port') {
            steps {
                sh "docker ps"
            }
        }
    }

    post {

        success {
            emailext(
                subject: "SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Build SUCCESSFUL..

Job: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}

Container is running.
Use 'docker ps' to find the mapped port.

Build URL:
${BUILD_URL}
""",
                to: "shanchalsenthil@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Build FAILED..

Job: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}

Check logs:
${BUILD_URL}
""",
                to: "shanchalsenthil@gmail.com"
            )
        }

        aborted {
            emailext(
                subject: "ABORTED: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Build ABORTED..

Reason:
Manual approval was rejected or build was stopped.

Build URL:
${BUILD_URL}
""",
                to: "shanchalsenthil@gmail.com"
            )
        }
    }
}
