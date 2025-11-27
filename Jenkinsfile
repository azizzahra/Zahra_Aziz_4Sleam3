pipeline {
    agent any

    environment {
        // Remplace azizzahra par ton login Docker Hub
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials') 
        IMAGE_NAME = "zahraaziz/student-management"   
        IMAGE_TAG = "${BUILD_NUMBER}"              
    }

    stages {
        stage('GIT') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/azizzahra/Zahra_Aziz_4Sleam3.git'
            }
        }

        stage('Compile & Package') {
            steps {
                dir('timesheet-devops') {          
                    sh 'mvn clean package -DskipTests'   
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('timesheet-devops') {         
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                    sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
