pipeline {
    agent any
    environment {
        IMAGE_NAME = 'yp3yp3/to_do_list'
        VERSION = "${BUILD_NUMBER}"
        email = 'yp3yp3@gmail.com'
        REMOTE_USER = 'ubuntu'
        REMOTE_HOST_STAGE = '172.31.40.99'
        REMOTE_HOST_PRODUCTION = '172.31.40.242'
        DB_HOST = '172.31.42.89'
        GITOPS_REPO = "yp3yp3/Todo_list-cd"
    }
    stages {
        stage('Build Docker Image') {
            when { not {branch 'main'} }
            steps {
                sh '''
                    docker build -t myapp ./app
                    docker tag myapp ${IMAGE_NAME}:${VERSION}
                    docker tag myapp ${IMAGE_NAME}:latest
                '''
            }
        }
        stage('Run app with Docker compose') {
            when { not {branch 'main'} }
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }
        stage('Run Tests') {
            when { not {branch 'main'} }
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
            when { not {branch 'main'} }
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
        stage('Update stage version') {
            when { not {branch 'main'} }
            steps {
                withCredentials([usernamePassword(credentialsId: 'github_token', passwordVariable: 'GH_TOKEN', usernameVariable: 'GH_USERNAME')]) {
                    script {
                        sh """
                            rm -rf gitops
                            git clone https://${GH_TOKEN}@github.com/${GITOPS_REPO} gitops
                            cd gitops
                            echo "${VERSION}" > stage_version.txt
                            git config user.name "jenkins"
                            git config user.email "ci@jenkins.local"
                            git add stage_version.txt
                            git commit -m "Update stage version to ${VERSION}"
                            git push origin main
                        """
                    }
                    }
                }
            }
            
        stage('Create PR to main') {
            when { not {branch 'main'} }
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GH_TOKEN')]) {
                    script {
                        def prTitle = "Merge ${BRANCH_NAME} into main @${VERSION}"
                        def prBody = "This PR merges changes from ${BRANCH_NAME} into main. http://stage.yp3yp3.online/"
                        def prUrl = "https://api.github.com/repos/yp3yp3/Todo_list/pulls"
                        sh """
                            curl -X POST \
                            -H "Authorization: token ${GH_TOKEN}" \
                            -H "Accept: application/vnd.github.v3+json" \
                            -d '{ \
                            \"title\": \"${prTitle}\", \
                            \"head\": \"${BRANCH_NAME}\", \
                            \"base\": \"main\", \
                            \"body\": \"${prBody}\" \
                            }' \
                            ${prUrl}
                        """
                    }
                }
            }
        }
        stage('update production version') {
            when { branch 'main' }
            steps {
                       // Extract version number from the latest Git commit message
                script {
                    def version = sh(
                        script: "git log -1 --pretty=%B | grep -oE '@[0-9]+' | tr -d '@'",
                        returnStdout: true
                    ).trim()

                    if (!version) {
                        error("❌ ERROR: No version number found in commit message. Make sure it includes @<number>.")
                    }

                    env.NEW_VERSION = version
                    echo "📦 Extracted version from commit: ${env.NEW_VERSION}"
                    withCredentials([usernamePassword(credentialsId: 'github_token', passwordVariable: 'GH_TOKEN', usernameVariable: 'GH_USERNAME')]) {
                    script {
                        sh """
                            rm -rf gitops
                            git clone https://${GH_TOKEN}@github.com/${GITOPS_REPO} gitops
                            cd gitops
                            echo "${NEW_VERSION}" > production_version.txt
                            git config user.name "jenkins"
                            git config user.email "ci@jenkins.local"
                            git add production_version.txt
                            git commit -m "Update production version to ${NEW_VERSION}"
                            git push origin main
                        """
                    }
                    }
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
                    message: "${JOB_NAME}.${BUILD_NUMBER} PASSED http://stage.yp3yp3.online/"
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
