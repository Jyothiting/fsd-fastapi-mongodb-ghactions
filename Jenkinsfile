pipeline {
    agent any

    environment {
        APP_SERVER = 'ubuntu@172.31.17.41'
        APP_DIR = '/home/ubuntu/fsd-fastapi-mongodb-ghactions/base-project-template-backup'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Test SSH Connection') {
            steps {
                echo 'Testing connection to Application Server...'

                sh """
                    ssh -o StrictHostKeyChecking=no ${APP_SERVER} 'echo SSH connection successful'
                """
            }
        }

        stage('Pull Latest Code') {
            steps {
                echo 'Pulling latest code on Application Server...'

                sh """
                    ssh ${APP_SERVER} '
                        cd ${APP_DIR} &&
                        git pull origin main
                    '
                """
            }
        }

        stage('Build Docker Images') {
            steps {
                echo 'Building Docker images on Application Server...'

                sh """
                    ssh ${APP_SERVER} '
                        cd ${APP_DIR} &&
                        docker compose build
                    '
                """
            }
        }

        stage('Stop Old Containers') {
            steps {
                echo 'Stopping old containers...'

                sh """
                    ssh ${APP_SERVER} '
                        cd ${APP_DIR} &&
                        docker compose down
                    '
                """
            }
        }

        stage('Start New Containers') {
            steps {
                echo 'Starting new containers...'

                sh """
                    ssh ${APP_SERVER} '
                        cd ${APP_DIR} &&
                        docker compose up -d
                    '
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking running containers...'

                sh """
                    ssh ${APP_SERVER} '
                        docker ps
                    '
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}
