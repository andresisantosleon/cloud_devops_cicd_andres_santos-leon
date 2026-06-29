pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        BRANCH = "${env.BRANCH_NAME}"
    }

    stages {
        stage('Declarative: Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Declarative: Tool Install') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Docker build') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:v1.0 .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                            docker stop nodemain || true
                            docker rm nodemain || true
                            docker run -d --name nodemain --expose 3000 -p 3000:3000 nodemain:v1.0
                        '''
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh '''
                            docker stop nodedev || true
                            docker rm nodedev || true
                            docker run -d --name nodedev --expose 3001 -p 3001:3000 nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed on branch: ${env.BRANCH_NAME}"
        }
        success {
            echo "Deployed successfully on branch: ${env.BRANCH_NAME}"
        }
    }
}