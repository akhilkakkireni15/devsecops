pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'vishwacloudlab'
        IMAGE_NAME = 'jenkins-docker-lab'
    }
    stages {
        stage('Clean up image and container') {
            steps {
                script {
               //     sh 'git clone git@github.com:Vishwanathms/t7.14-py-jenkins.git'
                    sh 'docker rm  jenkins_app -f || true'
                    sh 'docker image rmi $DOCKERHUB_USER/$IMAGE_NAME:latest || true'
                }
            }  
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest .'
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