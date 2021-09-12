pipeline {
  agent any

  stages {
    stage('Build & Test') {
      agent {
        docker {
          image 'php:7.4'
          args '-u root:sudo'
          reuseNode true
        }
      }

      steps {
        sh '''apt-get update -q
        apt-get install git unzip -y
        apt-get autoremove graphviz -y
        apt-get install graphviz -y
        '''

        sh 'php -r "copy(\'https://getcomposer.org/installer\', \'composer-setup.php\');"'
        sh 'php composer-setup.php'
        sh 'php composer.phar install --no-interaction'

        sh 'cp .env.example .env'

        sh 'php artisan key:generate'

        sh 'phpdbg -dmemory_limit=4G -qrr vendor/bin/phpunit -c phpunit.xml --log-junit ./results/results.xml --coverage-clover ./results/coverage.xml'
      }
    }

    stage('Sonarqube analysis') {
      environment {
        scannerHome = tool 'sonarqube-scanner'
      }

      steps {
        withSonarQubeEnv(installationName: 'sonarqube') {
          sh '$scannerHome/bin/sonar-scanner'
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
        }
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
