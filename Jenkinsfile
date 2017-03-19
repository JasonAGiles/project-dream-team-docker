#!/usr/bin/env groovy

def performOnDockerServer(closure) {
  withCredentials([string(credentialsId: 'aws-docker-sandbox-url', variable: 'dockerUrl')]) {
    docker.withServer(dockerUrl, 'aws-docker-sandbox-certificate') {
      closure()
    }
  }
}

def output

node {
  stage('Checkout') {
    checkout scm
  }

  stage('Build') {
    performOnDockerServer() {
      output = docker.build 'project-dream-team-docker'
    }
  }

  stage ('Test') {
    performOnDockerServer() {
      sh 'docker run --rm project-dream-team-docker tests.py'
    }
  }

  stage ('Push') {
    performOnDockerServer() {
      docker.withRegistry('hub.docker.com', 'jagiles-docker-registry') {
        output.push('project-dream-team-docker:latest')
      }
    }
  }

  stage ('Deploy') {
    performOnDockerServer() {
      withCredentials([file(credentialsId: 'project-dream-team-docker-compose-prod', variable: 'productionOverride')]) {
        sh "docker stack deploy --compose-file=docker-compose.yml --compose-file=${productionOverride} project-dream-team-docker"
      }
    }
  }
}
