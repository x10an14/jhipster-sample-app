pipeline {
    agent none
    options {
        skipDefaultCheckout()
    }
  stages {
      stage('Checkout') {
          agent any
          steps {
              checkout scm
              stash(name: 'ws', includes: '**')
          }
      }
    stage('Build Backend') {
        agent {
            docker {
                image 'maven:3-alpine'
                args '-v $HOME/.m2:/root/.m2'
            }
        }
      steps {
          unstash 'ws'
          sh './mvnw -B -DskipTests=true clean compile package'
          stash name: 'war', includes: 'target/**/*.war'
      }
    }
    stage('Test Backend') {
      steps {
        echo 'testing the bits'
      }
    }
    stage('More Tests') {
      steps {
        echo 'some more'
      }
    }
  }
}
