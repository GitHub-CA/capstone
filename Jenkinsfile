pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'echo "Hello World"'
        sh '''
                     echo "Multiline shell steps works too"
                     ls -lah
                 '''
      }
    }

    stage('Lint HTML') {
      steps {
        sh 'tidy -q -e *.html'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          customImage = docker.build("capstone")
        }

      }
    }

    stage('Push Docker image') {
      steps {
        script {
          docker.withRegistry( 'https://622635165270.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:ecr-user' ) {
            customImage.push()
          }
        }

      }
    }

    stage('Upload to AWS') {
      steps {
        withAWS(region: 'us-west-2', credentials: 'eks-user') {
  			sh 'aws eks --region us-west-2 update-kubeconfig --name capstone'
        }
      }

    }
  }
}
