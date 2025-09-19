pipeline {
    agent any

    environment {
        BACKEND_IMAGE = "s109-backend:latest"
        FRONTEND_IMAGE = "s109-frontend:latest"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            dir('backend') {
                steps {
                    sh 'docker build -t backend -f Dockerfile.backend .'
                }
            }
        }

        stage('Build Frontend Image') {
            dir('frontend') {
                steps {
                    sh 'docker build -t frontend -f Dockerfile.frontend .'
                }
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                echo 'Force removing old containers...'
                sh '''
                for c in ecom-backend ecom-frontend ecommerce-db; do
                    if [ $(docker ps -aq -f name=$c) ]; then
                        echo "Removing container $c..."
                        docker rm -f $c
                    else
                        echo "Container $c does not exist, skipping..."
                    fi
                done

                # Remove old networks and orphaned volumes
                docker-compose down -v --remove-orphans
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
            echo 'Cleaning unused images/volumes...'
            sh 'docker system prune -f'
        }

        success {
            echo 'Pipeline finished successfully!'
        }

        failure {
            echo 'Pipeline failed. Check logs above for details.'
        }
    }
}
