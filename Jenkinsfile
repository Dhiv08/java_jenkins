pipeline {
  agent { label 'linux-podman' }   // IMPORTANT: Jenkins node label that has podman installed
  tools {
    jdk 'JDK17'
    maven 'Maven_3.9.13'
  }
  options { timestamps() }

  environment {
    DOCKERHUB_REPO = "yourdockerhubuser/yourapp"  // CHANGE ME
    IMAGE_TAG = "${env.BUILD_NUMBER}"
    IMAGE = "${DOCKERHUB_REPO}:${IMAGE_TAG}"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test') {
      steps { sh 'mvn -B clean test' }
      post {
        always {
          junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
        }
      }
    }

    stage('Package') {
      steps { sh 'mvn -B package -DskipTests' }
      post { success { archiveArtifacts artifacts: 'target/*.jar', fingerprint: true } }
    }

    stage('Build Image (Podman)') {
      steps { sh 'podman build -t ${IMAGE} .' }
    }

    stage('Push Image (Docker Hub)') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-creds',
          usernameVariable: 'DH_USER',
          passwordVariable: 'DH_PASS'
        )]) {
          sh '''
            set -euo pipefail
            echo "$DH_PASS" | podman login docker.io -u "$DH_USER" --password-stdin
            podman push ${IMAGE} docker://${IMAGE}
            podman logout docker.io
          '''
        }
      }
    }

    stage('Scan Image (Trivy)') {
      steps {
        sh 'trivy image --no-progress --severity HIGH,CRITICAL --exit-code 1 ${IMAGE}'
      }
    }
  }
}

