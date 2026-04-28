pipeline {
agent any

stages {

    stage('Deploy') {
        steps {
            sh '''
            cd /var/lib/jenkins/ot-microservices-2

            git pull || true

            sudo pkill employee-api || true
            sudo pkill gunicorn || true

            # Employee
            cd services/employee
            go build -o employee-api
            setsid ./employee-api > employee.log 2>&1 < /dev/null &

            # Attendance
            cd ../attendance

            python3 -m venv venv
            . venv/bin/activate

            pip install --upgrade pip
            pip install flask flasgger flask-caching prometheus-flask-exporter psycopg2-binary python-json-logger gunicorn voluptuous pyyaml redis peewee

            setsid gunicorn app:app --log-config log.conf -b 0.0.0.0:8082 > attendance.log 2>&1 < /dev/null &
            '''
        }
    }

    stage('Health Check') {
        steps {
            sh '''
            sleep 20

            curl http://localhost:8081/api/v1/employee/health
            curl http://localhost:8082/api/v1/attendance/health
            '''
        }
    }
}

}
