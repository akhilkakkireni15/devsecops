pipeline {
    agent {
        label 'akhil-security-agent'
    }
    triggers {
    pollSCM('H/5 * * * *') // checks every 5 mins, or
    // OR use GitHub webhook instead (recommended)
  }

    environment {
        DOCKERHUB_USER = 'akhil' // must be lowercase
        IMAGE_NAME     = 'jenkins-docker-lab'
        SONARQUBE = 'SonarCloud'
        SONAR_TOKEN = credentials('akhil-sonar')
    }
stages {
          stage('Checkout') {
            steps {
                checkout scm
            }
        }
                stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('SonarCloud') {

                    sh '''

                        # Ensure we're in the root where sonar-project.properties exists

                        cd ${WORKSPACE}

                        sonar-scanner \

                          -Dsonar.projectBaseDir=python-app \

                          -Dsonar.login=$SONAR_TOKEN

                    '''

                }

            }

        }
 
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
                    sh 'docker build -t $DOCKERHUB_USER/$IMAGE_NAME:latest python-app'
                }
            }
        }
        stage('Scan Docker Image with Trivy') {
            steps {
                // Scan and save report
                sh '''
                  mkdir -p trivy-reports
                  trivy image --no-progress --exit-code 0 --format table -o trivy-reports/report.txt $DOCKERHUB_USER/$IMAGE_NAME
                  cat trivy-reports/report.txt
                '''
            }
        }
 
        stage('Archive Trivy Report') {
            steps {
                archiveArtifacts artifacts: 'trivy-reports/report.txt', fingerprint: true
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