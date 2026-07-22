pipeline {

    agent any

    environment {
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
                dir('app/employee-api') {
                    sh '''
                    echo "Stopping previous deployment..."

                    docker compose down || true

                    echo "Starting new deployment..."

                    docker compose up -d

                    echo "Deployment containers started"
                    '''
                }
            }
        }


        stage('Health Check') {
            steps {
                sh '''
                echo "Waiting for Employee API startup..."

                sleep 40

                echo "Checking Employee API..."

                curl -i http://localhost:8080/api/employees

                if [ $? -eq 0 ]
                then
                    echo "Employee API is running successfully"
                else
                    echo "Employee API health check failed"
                    docker logs employee-api
                    exit 1
                fi
                '''
            }
        }


        stage('Deployment Verification') {
            steps {
                sh '''
                echo "Running containers:"
                docker ps

                echo "Testing API response:"
                curl http://localhost:8080/api/employees
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
