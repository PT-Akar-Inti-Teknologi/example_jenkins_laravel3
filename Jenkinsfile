pipeline {
  agent any
  stages {
    stage('Build') {
      agent {
        docker {
          image: 'composer'
          reuseNode true
        }
      }

      steps {
        sh 'composer install --no-interaction'
      }
    }
    stage('Build & Test') {
      agent {
        docker {
          image 'php:7.4'
          args '-u root:sudo'
          reuseNode true
        }
      }

      steps {
        // sh '''apt-get update -q
        // apt-get install git -y
        // apt-get autoremove graphviz -y
        // apt-get install graphviz -y
        // '''

        // sh 'php -r "copy(\'https://getcomposer.org/installer\', \'composer-setup.php\');"'
        // sh 'php composer-setup.php'
        // sh 'php composer.phar install --no-interaction'

        // sh 'vendor/bin/phpunit'

        sh 'php artisan key:generate'
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
