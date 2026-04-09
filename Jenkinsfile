pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'docker ps'
            }
        }

        stage('Build & Deploy') {
            steps {
                sh 'docker-compose up -d --build'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'docker ps'
            }
        }

    }

    post {
        success {
            echo '✅ Deployment Successful!'
        }
        failure {
            echo '❌ Deployment Failed!'
        }
    }
}
