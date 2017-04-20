pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
          git 'https://github.com/rtyler/jhipster-sample-app'
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
    stage('Tests') {
        steps {
            parallel(
                'Backend': {
                    node {
                        unstash 'ws'
                        docker.image('maven:3-alpine').inside('-v /root/.m2:/root/.m2') {
                            sh './mvnw -B test package'
                        }                        
                        junit '**/surefire-reports/**/*.xml'
                        stash name: 'war', includes: 'target/**/*.war'
                    }
            },
                'Frontend': {
                    node {
                        unstash 'ws'
                        docker.image('node:alpine').inside {
                            sh 'yarn install'
                            sh 'yarn global add gulp-cli'
                            sh 'gulp test'
                        }
                    }
            },
                'Performance': {
                    node {
                        unstash 'ws'
                        docker.image('maven:3-alpine').inside('-v /root/.m2:/root/.m2') {
                            sh './mvnw -B gatling:execute'
                        }
                    }
            })
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
            unstash 'war'
            sh './mvnw -B docker:build'
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
            input message: 'Deploy to production?', ok: 'Fire zee missiles!'
            echo "Let's pretend a production deployment is happening"
        }
    }
  }
}
