pipeline {
  agent any

  stages {

    stage('Build Docker Image (on EC2)') {
      steps {
        sshagent(['ec2-ssh']) {
          bat '''
          ssh -o StrictHostKeyChecking=no ubuntu@52.66.142.4 ^
          "cd /home/ubuntu/jenkins_pipeline_1 && \
           git pull && \
           docker build -t myapp:latest ."
          '''
        }
      }
    }

    stage('Push Image to Docker Hub (from EC2)') {
      steps {
        sshagent(['ec2-ssh']) {
          bat '''
          ssh -o StrictHostKeyChecking=no ubuntu@52.66.142.4 ^
          "docker login -u glacierknight && \
           docker tag myapp:latest glacierknight/myapp:latest && \
           docker push glacierknight/myapp:latest"
          '''
        }
      }
    }

    stage('Deploy to Kubernetes (on EC2)') {
      steps {
        sshagent(['ec2-ssh']) {
          bat '''
          ssh -o StrictHostKeyChecking=no ubuntu@52.66.142.4 ^
          "kubectl apply -f /home/ubuntu/jenkins_pipeline_1/k8s && \
           kubectl rollout restart deployment myapp"
          '''
        }
      }
    }

  }
}
