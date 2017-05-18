pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        stash(name: 'ws', includes: '**')
      }
    }
    stage('Build Backend') {
      steps {
        unstash 'ws'
      }
    }
  }
}