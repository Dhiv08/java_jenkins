pipeline {
  agent any

  tools {
    jdk 'JDK17'
    maven 'Maven_3.9.13'
  }

  options { timestamps() }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test') {
      steps { bat 'mvn -B clean test' }
      post {
        always {
          junit testResults: 'target\\surefire-reports\\*.xml', allowEmptyResults: true
        }
      }
    }

    stage('Package') {
      steps { bat 'mvn -B package' }
      post { success { archiveArtifacts artifacts: 'target\\*.jar', fingerprint: true } }
    }
  }
}

