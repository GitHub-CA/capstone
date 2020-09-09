pipeline {
  agent any
  stages {
    stage('Updating config file') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
          sh 'aws eks --region us-west-2 update-kubeconfig --name capstone-app'
        }

      }
    }

    stage('Deploy to K8S') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
          sh 'kubectl set image deployment capstone-deployment capstone=capstone:latest'
        }

      }
    }

  }
}