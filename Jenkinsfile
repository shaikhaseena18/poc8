pipeline {
    agent any
 
    environment {
        IMAGE_NAME = "poc-8"
        IMAGE_TAG  = "${BUILD_NUMBER}"
        CONTAINER  = "poc-8"
 
        SONARQUBE_SERVER = "Sonar-Server"
        SONAR_SCANNER    = "sonar-scanner"
        SONAR_PROJECT_KEY = "POC-8-DevOps"
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shaikhaseena18/poc8.git'
            }
        }
 
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scanner = tool name: "${SONAR_SCANNER}", type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv("${SONARQUBE_SERVER}") {
                        sh """
                            ${scanner}/bin/sonar-scanner \
                            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                            -Dsonar.sources=.
                        """
                    }
                }
            }
        }
 
        stage('Build Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
 
        stage('Deploy') {
            steps {
                sh """
                    docker rm -f ${CONTAINER} || true
                    docker run -d -p 85:80 --name ${CONTAINER} ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }
 
    post {
        success { echo "Pipeline completed successfully" }
        failure { echo "Pipeline failed" }
    }
}
