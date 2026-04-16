pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Lint / Validate') {
            steps {
                sh 'echo "HTML files present:" && ls *.html'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t my-static-app:latest .'
            }
        }
        stage('Docker Test') {
            steps {
                sh 'docker run -d --name test-app -p 9090:80 my-static-app:latest'
                sh 'sleep 3'
                sh 'curl -f http://localhost:9090 | grep -i "<html"'
                sh 'docker stop test-app && docker rm test-app'
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
                sh 'kubectl rollout status deployment/static-web-deployment'
            }
        }
    }
    post {
        success {
            echo 'Deployment successful. Visit http://localhost:30080'
        }
        failure {
            echo 'Pipeline failed. Check console output.'
        }
    }
}