pipeline {
  agent any

  tools {
    maven 'Maven_3.9.13'
  }

  environment {
    APP_IMAGE = "java-jenkins-app"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

stage('Build JAR') {
  steps {
    bat '''
      mvn -v
      mvn -U -DskipTests clean package
      dir target
    '''
  }
}

    stage('Build Docker Image') {
      steps {
        script {
          def img = "${env.APP_IMAGE}:${env.BUILD_NUMBER}"
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
