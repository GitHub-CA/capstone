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
          customImage = docker.build("capstone")
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

  }
}
