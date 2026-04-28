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

        stage('Init Scylla DB') {
            steps {
                sh '''
                sleep 20
                docker exec scylla cqlsh -e "CREATE KEYSPACE IF NOT EXISTS employee_db WITH replication = {'class':'SimpleStrategy','replication_factor':1};"
                '''
            }
        }

        stage('Run Attendance Migration') {
            steps {
                sh '''
                docker exec postgres psql -U postgres -c "CREATE DATABASE attendance_db;" || true
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

        stage('Restart Services') {
            steps {
                sh '''
                docker restart employee attendance
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
