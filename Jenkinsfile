pipeline {
    agent none

    environment {
        IMAGE_NAME = 'adw4ith/isec6000-assessment2-node-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Install Dependencies') {
            agent {
                docker {
                    image 'node:16-bullseye'
                    reuseNode true
                }
            }

            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm ci'
            }
        }

        stage('Unit Tests') {
            agent {
                docker {
                    image 'node:16-bullseye'
                    reuseNode true
                }
            }

            steps {
                sh 'npm test'
            }
        }

        stage('Dependency Security Scan') {
            agent {
                docker {
                    image 'node:16-bullseye'
                    reuseNode true
                }
            }

            steps {
                sh 'npm audit --audit-level=high'
            }
        }

        stage('Build Docker Image') {
            agent any

            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Docker Image') {
            agent any

            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_TOKEN'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_TOKEN" | docker login \
                        -u "$DOCKER_USER" \
                        --password-stdin

                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Assessment 2 pipeline execution completed.'
        }

        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Review the Jenkins console logs.'
        }
    }
}

