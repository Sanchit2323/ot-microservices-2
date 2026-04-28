pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker-compose build
                '''
            }
        }

        stage('Deploy Services') {
            steps {
                sh '''
                docker-compose down
                docker-compose up -d
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 10
                curl http://localhost:8081/api/v1/employee/health
                curl http://localhost:8082/api/v1/attendance/health
                '''
            }
        }
    }
}
