pipeline {
agent any

stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
        }
    }

    stage('Stop Old Services') {
        steps {
            sh '''
            sudo pkill employee-api || true
            sudo pkill gunicorn || true
            '''
        }
    }

    stage('Build & Start Employee') {
        steps {
            sh '''
            cd services/employee

            go build -o employee-api

            nohup ./employee-api > employee.log 2>&1 &
            '''
        }
    }

    stage('Start Attendance') {
        steps {
            sh '''
            cd services/attendance

            source venv/bin/activate

            nohup gunicorn app:app --log-config log.conf -b 0.0.0.0:8082 > attendance.log 2>&1 &
            '''
        }
    }

    stage('Health Check') {
        steps {
            sh '''
            sleep 10

            echo "Checking Employee..."
            curl -f http://localhost:8081/api/v1/employee/health

            echo "Checking Attendance..."
            curl -f http://localhost:8082/api/v1/attendance/health
            '''
        }
    }
}

}
