pipeline {

    agent any

    environment {
        APP_NAME = "employee-api"
        IMAGE_NAME = "employee-api:latest"
        CONTAINER_NAME = "employee-api"
        NETWORK_NAME = "devsecops-network"
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
                    sh '''
                    chmod +x mvnw
                    ./mvnw clean package -DskipTests
                    '''
                }
            }
        }


        stage('Build Docker Image') {
            steps {
                dir('app/employee-api') {
                    sh '''
                    docker build -t ${IMAGE_NAME} .
                    '''
                }
            }
        }


        stage('Deploy Application') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true

                docker network create ${NETWORK_NAME} || true

                docker run -d \
                --name ${CONTAINER_NAME} \
                --network ${NETWORK_NAME} \
                -p 8080:8080 \
                ${IMAGE_NAME}
                '''
            }
        }


        stage('Health Check') {
            steps {
                sh '''
                sleep 15

                curl -f http://localhost:8080/api/employees

                echo "Employee API is running successfully"
                '''
            }
        }

    }


    post {

        success {
            echo "Deployment completed successfully!"
        }

        failure {
            echo "Deployment failed!"
        }

    }
}
