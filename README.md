pipeline {
    agent any

    environment {
        IMAGE_NAME = "html-site"
        VERSION = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/<your-username>/<your-repo>.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${VERSION} ."
            }
        }

        stage('Deploy Website') {
            steps {
                script {
                    sh "docker rm -f ${IMAGE_NAME} || true"
                    sh "docker run -d --name ${IMAGE_NAME} -p 8080:80 ${IMAGE_NAME}:${VERSION}"
                }
            }
        }
    }

    post {
        success {
            echo '✅ Website deployed at http://localhost:8080'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
# web-app
