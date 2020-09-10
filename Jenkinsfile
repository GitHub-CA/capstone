pipeline {
  agent any

  parameters {
    string(defaultValue: "", description: 'latest dev sha', name='TAG')
  }
  
  stages {
    stage('Updating config file') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
          sh '''aws eks --region us-west-2 update-kubeconfig --name capstone
docker image history mbeimcik/capstone'''
        }
      }
    }

    stage('Rolling update K8S') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
         sh '''
           kubectl set image deployment capstone capstone=mbeimcik/capstone:{TAG}'''
        }
      }
    }

    stage('Rolling update status') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
          sh 'kubectl rollout status deployment capstone'
        }
      }
    }
  }
}
