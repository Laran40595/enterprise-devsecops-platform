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
                    echo "Stopping old containers..."

                    docker compose down || true

                    echo "Starting application..."

                    docker compose up -d

                    echo "Containers started"
                    '''
                }
            }
        }


        stage('Health Check') {
            steps {
                sh '''
                echo "Waiting for Employee API..."

                for i in {1..10}
                do
                    if curl -f http://localhost:8080/api/employees
                    then
                        echo ""
                        echo "Employee API is running successfully"
                        exit 0
                    fi

                    echo "API not ready yet. Attempt $i/10"
                    sleep 10
                done

                echo "Employee API health check failed"
                docker ps
                exit 1
                '''
            }
        }


        stage('Deployment Verification') {
            steps {
                sh '''
                echo "Running containers:"
                docker ps

                echo "Testing API:"
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
