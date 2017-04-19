pipeline {
  agent none
  stages {
    stage('Build') {
      agent { docker 'maven:3-alpine' }
      steps {
        sh 'ls'
      }
    }
  }
}
