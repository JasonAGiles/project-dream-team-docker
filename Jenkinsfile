#!/usr/bin/env groovy

def performOnDockerServer(closure) {
  withCredentials([string(credentialsId: 'aws-docker-sandbox-url', variable: 'dockerUrl')]) {
    docker.withServer(dockerUrl, 'aws-docker-sandbox-certificate') {
      closure()
    }
  }
}

node {
  stage('Checkout') {
    checkout scm
  }

  stage('Build') {
    performOnDockerServer() {
      docker.build 'project-dream-team-docker'
    }
  }

  stage ('Test') {
    performOnDockerServer() {
      sh 'docker run --rm project-dream-team-docker tests.py'
    }
  }
}
