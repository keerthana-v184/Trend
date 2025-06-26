pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "iamkeerthana/trend-app"
        DOCKER_CREDENTIALS_ID = 'dockerhub-creds'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: 'github-creds', url: 'https://github.com/keerthana-v184/Trend.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Login & Push to DockerHub') {
            steps {
                script {
                    docker.withRegistry('', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_IMAGE}").tag('v1')
                        docker.image("${DOCKER_IMAGE}:v1").push()
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh '''
                        aws eks --region us-east-1 update-kubeconfig --name trend-cluster
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                        kubectl get svc
                    '''
                }
            }
        }
    }
}

