pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "zahraaziz/student-management"
        IMAGE_TAG = "${BUILD_NUMBER}"
        SONAR_TOKEN = credentials('sonarqube-token') // ID du token dans Jenkins
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
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube-local') { // nom du serveur configuré
                    sh '''
                        mvn sonar:sonar 
                          -Dsonar.projectKey=student-management-zahra 
                          -Dsonar.host.url=http://localhost:9000 
                          -Dsonar.login=${SONAR_TOKEN}
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
        stage('Deploy to Kubernetes') {
          steps {
            sh 'kubectl apply -f k8s/mysql-deployment.yaml -n devops || true'
            sh 'kubectl apply -f k8s/spring-deployment.yaml -n devops'
            sh 'kubectl rollout status deployment/spring-app -n devops'
          }
        }
    }
    post {
        always {
            sh 'docker logout || true'
        }
    }
}
