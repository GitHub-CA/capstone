pipeline {
  agent any
  
  def tag = ""

  stages {
    stage('Lint HTML') {
      steps {
        sh 'tidy -q -e *.html'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          tag = """${sh(
            returnStdout: true,
            script: 'git log -1 --pretty=%h'
          )}""".trim()
          customImage = docker.build("mbeimcik/capstone:${tag}")
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

    stage('Start another job') {
      steps {
        build job: 'Deployment', wait: false, parameters: [string(name: 'TAG', value: String.valueOf(tag))]
      }

    }
 }
}
