pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "frontend"
        BACKEND_IMAGE = "backend"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/VamsiKLU/CICDLab.git'
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t $BACKEND_IMAGE -f Dockerfile.backend .'
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t $FRONTEND_IMAGE -f Dockerfile.frontend .'
                }
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                    echo "Force removing old containers..."
                    docker rm -f ecom-backend ecom-frontend ecom-db || true
                    docker-compose down -v --remove-orphans || true
                '''
            }
        }

        stage('Run with Docker Compose') {
            steps {
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
            echo "Cleaning unused images/volumes..."
            sh 'docker system prune -f'
        }
    }
}
