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
            '''
        }
    }

    stage('Build & Start Employee') {
        steps {
            sh '''
            cd services/employee
            sleep 15

            go build -o employee-api

            nohup ./employee-api > employee.log 2>&1 &
            '''
        }
    }

    stage('Start Attendance') {
        steps {
            sh '''
            cd services/attendance
            python3 -m venv venv || true

            . venv/bin/activate

            pip install --upgrade pip
            pip install flask flasgger flask-caching prometheus-flask-exporter psycopg2-binary python-json-logger gunicorn voluptuous pyyaml redis peewee

            nohup gunicorn app:app --log-config log.conf -b 0.0.0.0:8082 > attendance.log 2>&1 &
            '''
        }
    }

    stage('Health Check') {
        steps {
            sh '''
            sleep 10

            for i in {1..5}
            do 
              sleep 5 
              curl http://localhost:8081/api/v1/employee/health && break 
            done

            echo "Checking Attendance..."
            for i in {1..5}
            do 
              sleep 5 
              curl http://localhost:8082/api/v1/attendance/health && break 
            done
            '''
        }
    }
}

}
