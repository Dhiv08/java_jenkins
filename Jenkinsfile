pipeline {
  agent any

  tools {
    maven 'M3'   // <-- change 'M3' to your Maven installation name in Jenkins
  }

  environment {
    APP_IMAGE = "java-jenkins-app"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build JAR (Maven)') {
      steps {
        bat '''
          mvn -v
          mvn -U -DskipTests clean package
          dir target
        '''
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          def img = "${env.APP_IMAGE}:${env.BUILD_NUMBER}"   // fixes your 'def' warning
          bat """
            docker version
            docker build -t ${img} .
            docker images | findstr ${env.APP_IMAGE}
          """
        }
      }
    }
  }
}
