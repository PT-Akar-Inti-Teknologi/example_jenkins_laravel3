pipeline {
    agent any
    stages {
      stage('Build & Test') {
        agent {
          docker {
            image 'composer'
            args '-u root:sudo'
            reuseNode true
          }
        }

        steps {
          sh 'composer install --no-interaction'

          sh 'vendor/bin/phpunit'
        }
      }
    }

  post {
    failure {
      emailext subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}",
        body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
        recipientProviders: [
          [$class: 'DevelopersRecipientProvider'],
          [$class: 'RequesterRecipientProvider']
        ]
    }

    always {
      cleanWs()
    }
  }
}
