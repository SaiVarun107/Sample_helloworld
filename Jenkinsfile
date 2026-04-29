pipeline {
  agent any

  environment {
    KUBECONFIG = credentials('kubeconfig-creds')
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t myapp:latest .'
      }
    }

    stage('Push Image to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'docker-creds',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker tag myapp:latest $DOCKER_USER/myapp:latest
            docker push $DOCKER_USER/myapp:latest
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl apply -f k8s/
          kubectl rollout restart deployment myapp
        '''
      }
    }
  }
}
