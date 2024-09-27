pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' // Your Docker credentials ID
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/rakeshrampalli/MyWebApp.git', branch: 'main'
            }
        }
        
        stage('Build with Maven') {
            steps {
                dir('.') { // Ensure this points to the directory containing pom.xml
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t rakeshrampalli/mywebapp:latest .'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        sh 'docker push rakeshrampalli/mywebapp:latest'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Start Minikube
                    sh 'minikube start'

                    // Deploy the application to Kubernetes
                    sh '''
                    kubectl apply -f k8s/deployment.yaml --validate=false
                    kubectl apply -f k8s/service.yaml --validate=false
                    '''
                }
            }
        }
    }
}
