pipeline {

    agent any

    environment {
        APP_NAME = "employee-api"
        IMAGE_NAME = "employee-api:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Laran40595/enterprise-devsecops-platform.git'
            }
        }

        stage('Build Spring Boot Application') {
            steps {
                dir('app/employee-api') {
                    sh './mvnw clean package -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app/employee-api') {
                    sh 'docker build -t ${IMAGE_NAME} .'
                }
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                docker stop ${APP_NAME} || true
                docker rm ${APP_NAME} || true

                docker run -d \
                --name ${APP_NAME} \
                --network devsecops-network \
                -p 8080:8080 \
                ${IMAGE_NAME}
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 10
                curl http://localhost:8080/api/employees
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }

        failure {
            echo "Deployment failed!"
        }
    }
}
