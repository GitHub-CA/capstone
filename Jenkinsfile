def TAG = ""

pipeline {
  agent any
  
  stages {
    
    stage('Lint HTML') {
      steps {
        sh 'tidy -q -e *.html'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          TAG = """${sh(
            returnStdout: true,
            script: 'git log -1 --pretty=%h'
          )}""".trim()
          customImage = docker.build("mbeimcik/capstone:${TAG}")
        }

      }
    }

    stage('Push Docker image') {
      steps {
        script {
          docker.withRegistry( 'https://index.docker.io/v2/', 'docker-hub' ) {
            customImage.push()
          }
        }

      }
    }

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
           tag=$(git log -1 --pretty=%h)
           kubectl set image deployment capstone capstone=mbeimcik/capstone:$tag'''
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
