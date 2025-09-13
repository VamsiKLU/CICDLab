pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        APP_NAME = 'ecommerce'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/VamsiKLU/CICDLab.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                sh 'docker build -t ecommerce-backend -f Dockerfile.backend .'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t ecommerce-frontend -f Dockerfile.frontend .'
            }
        }

        stage('Run with Docker Compose') {
            steps {
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
            }
        }

        stage('Verify Containers') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        always {
            echo "Cleaning old containers..."
            sh 'docker system prune -f'
        }
    }
}
