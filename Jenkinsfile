pipeline {

    agent any

    environment {
        APP_NAME = "employee-api"
        IMAGE_NAME = "employee-api:latest"
        NETWORK_NAME = "devsecops-network"
        COMPOSE_FILE = "app/employee-api/docker-compose.yml"
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



        stage('Deploy With Docker Compose') {

            steps {

                sh '''

                cd app/employee-api

                docker compose down || true

                docker compose up -d

                '''

            }

        }



        stage('Health Check') {

            steps {

                sh '''

                sleep 20

                curl -f http://localhost:8080/api/employees

                echo "Employee API deployment successful"

                '''

            }

        }


    }



    post {

        success {

            echo "CI/CD Pipeline completed successfully"

        }


        failure {

            echo "Pipeline failed"

        }

    }

}
