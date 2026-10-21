pipeline {
    agent any

    environment {
     DOCKER_IMAGE = "lakshminarayananr0411/inventory-control-system:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Files') {
            steps {
                bat 'if not exist index.html exit /b 1'
                bat 'if not exist shared\\style.css exit /b 1'
                bat 'if not exist dashboard.html exit /b 1'
                echo 'Inventory Control System files are present.'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f deployment.yaml'
                bat 'kubectl rollout status deployment/inventory-control-system --timeout=120s'
            }
        }
    }

    post {
        success {
            echo 'Inventory Control System CI/CD completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Open Console Output and check the first error.'
        }
        
    }
}
