pipeline {
    agent any

    environment {
        DOCKER_BUILDKIT = 1
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                dir('backend') {
                    sh 'docker build -t s109-backend -f Dockerfile.backend .'
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                dir('frontend') {
                    sh 'docker build -t s109-frontend -f Dockerfile.frontend .'
                }
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                    echo 'Stopping and removing old containers if they exist...'
                    docker rm -f ecom-backend ecom-frontend ecommerce-db || true
                    docker-compose down -v --remove-orphans || true
                '''
            }
        }

        stage('Run with Docker Compose') {
            steps {
                sh '''
                    echo 'Starting containers using docker-compose...'
                    docker-compose up -d --build
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    echo 'Verifying running containers...'
                    docker ps
                '''
            }
        }
    }

    post {
        always {
            echo 'Cleaning up unused Docker images and volumes...'
            sh 'docker system prune -f'
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check logs above for errors.'
        }
    }
}
