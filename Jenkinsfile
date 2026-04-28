pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
            }
        }

        stage('Cleanup') {
            steps {
                sh '''
                docker rm -f scylla postgres employee attendance || true
                docker network rm microservice-pipeline_default || true
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('Start Databases Only') {
            steps {
                sh 'docker-compose up -d postgres scylla'
            }
        }

        stage('Init Scylla Keyspace') {
            steps {
                sh '''
                sleep 15
                docker exec scylla cqlsh -e "CREATE KEYSPACE IF NOT EXISTS employee_db WITH replication = {'class':'SimpleStrategy','replication_factor':1};"
                '''
            }
        }

        stage('Create Postgres DB') {
            steps {
                sh '''
                docker exec postgres psql -U postgres -c "CREATE DATABASE attendance_db;" || true
                '''
            }
        }

        stage('Run Attendance Migration') {
            steps {
                sh '''
                docker run --rm \
                --network microservice-pipeline_default \
                -v $(pwd)/services/attendance:/liquibase/changelog \
                -e LIQUIBASE_COMMAND_URL=jdbc:postgresql://postgres:5432/attendance_db \
                -e LIQUIBASE_COMMAND_USERNAME=postgres \
                -e LIQUIBASE_COMMAND_PASSWORD=postgres \
                -e LIQUIBASE_COMMAND_CHANGELOG_FILE=db.changelog-master.xml \
                liquibase/liquibase \
                --driver=org.postgresql.Driver \
                update
                '''
            }
        }

        stage('Start Applications') {
            steps {
                sh 'docker-compose up -d employee attendance'
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                echo "Checking Employee..."
                for i in {1..5}; do sleep 5; curl -f http://localhost:8081/api/v1/employee/health && break; done

                echo "Checking Attendance..."
                for i in {1..5}; do sleep 5; curl -f http://localhost:8082/api/v1/attendance/health && break; done
                '''
            }
        }
    }
}
