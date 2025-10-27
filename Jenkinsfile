pipeline {
    agent {
        label 'ak-docker-agent'
    }
    triggers {
    pollSCM('H/5 * * * *') // checks every 5 mins, or
    // OR use GitHub webhook instead (recommended)
  }

    environment {
        DOCKERHUB_USER = 'akhil' // must be lowercase
        IMAGE_NAME     = 'jenkins-docker-lab'
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
                    // Run Trivy scan using Docker
                    sh '''
                        set -e
                        echo "Updating Trivy image..."
                        docker pull aquasec/trivy:latest

                        echo "Running Trivy container scan..."
                        docker run --rm \
                            -v /var/run/docker.sock:/var/run/docker.sock \
                            -v $HOME/.cache/trivy:/root/.cache/ \
                            aquasec/trivy:latest image --no-progress \
                            --severity HIGH,CRITICAL \
                            $DOCKERHUB_USER/$IMAGE_NAME:latest
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