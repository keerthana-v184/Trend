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
            docker.image("${DOCKER_IMAGE}").push("latest")
          }
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh 'aws eks --region us-east-1 update-kubeconfig --name trend-cluster'
        sh 'kubectl apply -f deployment.yaml'
        sh 'kubectl apply -f service.yaml'
        sh 'kubectl get svc'
      }
    }
  }
}

