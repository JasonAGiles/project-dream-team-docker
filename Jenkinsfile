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

  stage ('Deploy') {
    performOnDockerServer() {
      withCredentials([file(credentialsId: 'project-dream-team-docker-compose-prod', variable: 'productionOverride')]) {
        sh "docker-compose -f docker-compose.yml -f ${productionOverride} build"
        sh "docker-compose -f docker-compose.yml -f ${productionOverride} up -d"
      }
    }
  }
}
