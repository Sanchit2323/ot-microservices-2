pipeline {
agent any

stages {

    stage('Checkout Code') {
        steps {
            git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
        }
    }

    stage('Deploy') {
        steps {
            sh '''
            cd /var/lib/jenkins/ot-microservices-2

            # Pull latest code
            git pull

            # Build Employee Service
            cd services/employee
            go build -o employee-api

            # Restart services using systemd
            sudo systemctl restart employee
            sudo systemctl restart attendance
            '''
        }
    }

    stage('Health Check') {
        steps {
            sh '''
            sleep 10

            echo "Checking Employee..."
            curl http://localhost:8081/api/v1/employee/health

            echo "Checking Attendance..."
            curl http://localhost:8082/api/v1/attendance/health
            '''
        }
    }
}

}
