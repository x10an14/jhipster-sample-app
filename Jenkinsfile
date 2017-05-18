pipeline {
  agent none
  stages {
      stage('Checkout') {
        agent any  
        steps {
          stash(name: 'ws', includes: '**')
        }
      }
      stage('Build Backend') {
          agent {
              docker {
                  image 'maven:3-alpine'
                  args '-v /home/rsandell/.m2:/root/.m2'
              }
          }
          steps {
              unstash 'ws'
              sh './mvnw -B -DskipTests=true clean compile package'
              stash name: 'war', includes: 'target/**/*.war'
          }
      }
  }
}
