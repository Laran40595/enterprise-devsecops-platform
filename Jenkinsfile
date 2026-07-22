pipeline {

    agent any

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
                        docker build -t employee-api:latest .
                    '''
                }
            }
        }


        stage('Deploy With Docker Compose') {
            steps {
                dir('app/employee-api') {
                    sh '''
                        docker compose down || true
                        docker compose up -d
                    '''
                }
            }
        }


        stage('Health Check') {
            steps {
                sh '''
                    sleep 20
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
