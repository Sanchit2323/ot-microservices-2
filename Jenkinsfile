pipeline {
    agent any

    environment {
        EC2_IP = "YOUR_EC2_PUBLIC_IP"
        EC2_USER = "ubuntu"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Sanchit2323/ot-microservices-2.git'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_IP} << EOF

                cd ~/ot-microservices-2 || git clone https://github.com/Sanchit2323/ot-microservices-2.git

                cd ~/ot-microservices-2
                git pull

                # 🔥 Stop old services
                pkill employee-api || true
                pkill gunicorn || true

                # 🔥 Start Employee (Go)
                cd services/employee
                go build -o employee-api
                nohup ./employee-api > employee.log 2>&1 &

                # 🔥 Start Attendance (Python)
                cd ../attendance
                source venv/bin/activate
                nohup gunicorn app:app --log-config log.conf -b 0.0.0.0:8082 > attendance.log 2>&1 &

                exit
                EOF
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 10

                curl http://${EC2_IP}:8081/api/v1/employee/health
                curl http://${EC2_IP}:8082/api/v1/attendance/health
                '''
            }
        }
    }
}
