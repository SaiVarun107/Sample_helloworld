pipeline {
  agent any

  stages {

    stage('Build Docker Image (on EC2)') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'ec2-ssh',
          keyFileVariable: 'SSH_KEY',
          usernameVariable: 'SSH_USER'
        )]) {
          bat '''
          REM --- Fix SSH key permissions for Windows OpenSSH ---
          icacls "%SSH_KEY%" /inheritance:r >nul
          icacls "%SSH_KEY%" /remove:g "BUILTIN\\Users" >nul
          icacls "%SSH_KEY%" /grant:r "Everyone:R" >nul

          REM --- Build on EC2 ---
          ssh -i "%SSH_KEY%" -o StrictHostKeyChecking=no %SSH_USER%@52.66.142.4 ^
          "cd /home/ubuntu/jenkins_pipeline_1 && \
           git pull && \
           docker build -t myapp:latest ."
          '''
        }
      }
    }

    stage('Push Image to Docker Hub (from EC2)') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'ec2-ssh',
          keyFileVariable: 'SSH_KEY',
          usernameVariable: 'SSH_USER'
        )]) {
          bat '''
          icacls "%SSH_KEY%" /inheritance:r >nul
          icacls "%SSH_KEY%" /remove:g "BUILTIN\\Users" >nul
          icacls "%SSH_KEY%" /grant:r "Everyone:R" >nul

          ssh -i "%SSH_KEY%" -o StrictHostKeyChecking=no %SSH_USER%@52.66.142.4 ^
          "docker tag myapp:latest glacierknight/myapp:latest && \
           docker push glacierknight/myapp:latest"
          '''
        }
      }
    }

    stage('Deploy to Kubernetes (on EC2)') {
      steps {
        withCredentials([sshUserPrivateKey(
          credentialsId: 'ec2-ssh',
          keyFileVariable: 'SSH_KEY',
          usernameVariable: 'SSH_USER'
        )]) {
          bat '''
          icacls "%SSH_KEY%" /inheritance:r >nul
          icacls "%SSH_KEY%" /remove:g "BUILTIN\\Users" >nul
          icacls "%SSH_KEY%" /grant:r "Everyone:R" >nul

          ssh -i "%SSH_KEY%" -o StrictHostKeyChecking=no %SSH_USER%@52.66.142.4 ^
          "kubectl apply -f /home/ubuntu/jenkins_pipeline_1/k8s && \
           kubectl rollout restart deployment myapp"
          '''
        }
      }
    }

  }
}
