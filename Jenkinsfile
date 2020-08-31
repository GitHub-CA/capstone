def customImage = ''
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
					customImage = docker.build("capstone:${env.BUILD_ID}")
				}
			}
		} 
		stage('Push Docker image') {
			steps {
				script {
					withCredentials([usernamePassword( credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
						sh "docker login -u ${USERNAME} -p ${PASSWORD}"
						customImage.push()
					}
				}
			}
		}
		stage('Upload to AWS') {
              steps {
                  withAWS(region:'us-east-2',credentials:'aws-static') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'udacity.project.3')
                  }
              }
         }
     }
}
