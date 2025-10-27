pipeline {
    agent {
        label 'ak-docker-agent'
    }

    environment {
        DOCKERHUB_USER = 'Akhil'
        IMAGE_NAME = 'jenkins-docker-lab'
    }

    stages {
        stage('Clean up image and container') {
            steps {
                script {
                    // Remove existing container and image if any
                    sh 'docker rm jenkins_app -f || true'
                    sh 'docker image rmi $DOCKERHUB_USER/$IMAGE_NAME:latest || true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest python-app'
                }
            }
        }

        stage('Trivy Security Scan') {
            steps {
                script {
                    // Run Trivy to scan the built image
                    sh '''
                        echo "Running Trivy security scan..."
                        trivy image --no-progress --severity HIGH,CRITICAL $DOCKERHUB_USER/$IMAGE_NAME:latest
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}