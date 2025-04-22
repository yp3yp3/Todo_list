pipeline {
    agent any
    environment {
        IMAGE_NAME = 'yp3yp3/to_do_list'
        VERSION = "${BUILD_NUMBER}"
        email = 'yp3yp3@gmail.com'
        REMOTE_USER = 'ubuntu'
        REMOTE_HOST = '172.31.43.59'
        DB_HOST = '172.31.42.89'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t myapp ./app
                    docker tag myapp ${IMAGE_NAME}:${VERSION}
                    docker tag myapp ${IMAGE_NAME}:latest
                '''
            }
        }
        stage('Run app with Docker compose') {
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                    python3 -m venv .venv
                    . .venv/bin/activate
                    pip install -r tests/requirements.txt
                    pytest ./tests
                '''
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    script {
                        docker.withRegistry('', 'docker-hub') {
                            docker.image("${IMAGE_NAME}").push("${VERSION}")
                            docker.image("${IMAGE_NAME}").push('latest')    
                        }
                    }
                   }
                }
            }
        stage('Deploy to Production') {
            steps {
                    sshagent (credentials: ['node1']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} \
                            "docker pull ${IMAGE_NAME}:${VERSION} && docker rm -f myapp && \
                            docker run -d --name myapp \
                            -e DB_NAME=todo -e DB_USER=myuser -e DB_PASSWORD=pass -e DB_HOST=${DB_HOST} \
                            -p 5000:5000 ${IMAGE_NAME}:${VERSION}"
                        """
                }
            }
        }
    }
        post {
             failure {
                slackSend(
                    channel: '#jenkins',
                    color: 'danger',
                    message: "${JOB_NAME}.${BUILD_NUMBER} FAILED"
                )
                
                emailext(
                    subject: "${JOB_NAME}.${BUILD_NUMBER} FAILED",
                    mimeType: 'text/html',
                    to: "$email",
                    body: "${JOB_NAME}.${BUILD_NUMBER} FAILED"
                )
            }
            success {
                slackSend(
                    channel: '#jenkins',
                    color: 'good',
                    message: "${JOB_NAME}.${BUILD_NUMBER} PASSED"
                )
                emailext(
                    subject: "${JOB_NAME}.${BUILD_NUMBER} PASSED",
                    mimeType: 'text/html',
                    to: "$email",
                    body: "${JOB_NAME}.${BUILD_NUMBER} PASSED"
                )
            }
            always {
                sh '''
                    docker compose down || true
                    docker rmi myapp || true
                '''
            }
        }
    }
