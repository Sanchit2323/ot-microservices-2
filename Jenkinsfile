pipeline {
agent any

```
stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
        }
    }

    stage('Deploy') {
        steps {
            sh '''
            pwd

            sudo pkill employee-api || true

            # Employee Service
            cd services/employee
            go build -o employee-api
            setsid ./employee-api > employee.log 2>&1 < /dev/null &

            # Attendance Service
            cd ../attendance
            python3 -m venv venv || true
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
```

}
