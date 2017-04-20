pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
          stash includes: '**', name: 'ws', useDefaultExcludes: false
      }
    }
    stage('Build Backend') {
        agent {
            docker {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            unstash 'ws'
            sh './mvnw -B clean compile'
        }
    }
    stage('Test Backend') {
        agent {
            docker {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            unstash 'ws'
            sh './mvnw -B test'
            junit '**/surefire-reports/**/*.xml'
        }
    }
    stage('Test Frontend') {
        agent { docker 'node:alpine' }
        steps {
            unstash 'ws'
            sh 'yarn install'
            sh 'yarn global add gulp-cli'
            sh 'gulp test'
        }
    }
    stage('Performance Tests') {
        agent {
            docker {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            unstash 'ws'
            sh './mvnw -B gatling:execute'
        }
    }
    stage('Build Container') {
        agent {
            docker {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2 -v /var/run/docker.sock:/var/run/docker.sock'
            }
        }
        steps {
            unstash 'ws'
            sh './mvnw -B package docker:build'
        }
    }
    stage('Deploy to Staging') {
        agent any
        steps {
            echo "Let's pretend a deployment is happening"
        }
    }
    stage('Deploy to production') {
        agent any
        steps {
            echo "Let's pretend a production deployment is happening"
        }
    }
  }
}
