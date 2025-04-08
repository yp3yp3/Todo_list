pipeline {
    agent any
    environment {
        IMAGE_NAME = 'to_do_list'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t myapp ./app
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
                    sh '''
                    echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin
                    docker tag myapp $DOCKER_USERNAME/$IMAGE_NAME:latest
                    docker push $DOCKER_USERNAME/$IMAGE_NAME:latest
                    '''
                    }
                }
            }
        }
    }



