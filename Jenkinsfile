pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
            }
        }

        stage('Cleanup Old Containers') {
            steps {
                sh '''
                docker rm -f scylla postgres employee attendance || true
                '''
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

        stage('Run Migrations') {
            steps {
                sh '''
                sleep 15

                # attendance migration
                docker exec attendance liquibase update --driver-properties-file=liquibase.properties || true
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
