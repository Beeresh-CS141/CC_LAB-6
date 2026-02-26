pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 -p 5001:5000 backend-app
                docker run -d --name backend2 -p 5002:5000 backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker build -t nginx-lb nginx

                docker run -d \
                --name nginx-lb \
                -p 80:80 \
                --link backend1 \
                --link backend2 \
                nginx-lb
                '''
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console logs.'
        }
    }
}
