pipeline {
    agent any

    environment {
        // Add any environment variables if needed
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/VamsiKLU/CICDLab.git']]
                ])
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
                    echo "Removing old containers if they exist..."
                    docker rm -f ecom-backend ecom-frontend ecom-db || true
                    docker-compose down -v --remove-orphans || true
                '''
            }
        }

        stage('Run with Docker Compose') {
            steps {
                sh '''
                    echo "Starting containers using docker-compose..."
                    docker-compose up -d --build
                '''
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
