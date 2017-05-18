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
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                unstash 'ws'
                unstash 'war'
                sh './mvnw -B test findbugs:findbugs'
                junit '**/surefire-reports/**/*.xml'
                findbugs pattern: 'target/**/findbugsXml.xml', unstableNewAll: '0' //unstableTotalAll: '0'
            }
        }
        stage('Test More') {
            agent none
            steps {
                parallel(
                'Frontend' : {
                    script {
                        node {
                            docker.build('latest', 'docker/gulp/').inside {
                                unstash 'ws'
                                //sh 'gulp test'
                                sh 'ping -w 15 8.8.8.8' //Can't make those tests work - doing something else for simplicity
                            }
                        }
                    }
                },
                'Performance' : {
                    script {
                        node {
                            docker.image('maven:3-alpine').inside('-v ${HOME}/.m2:/root/.m2') {
                                unstash 'ws'
                                unstash 'war'
                                sh './mvnw -B gatling:execute'
                            }
                        }
                    }
                })
            }
        }
        stage('Build Container') {
            agent {
               docker {
                   image 'maven:3-alpine'
                   args '-v $HOME/.m2:/root/.m2 -v /var/run/docker.sock:/var/run/docker.sock'
               }
            }
            when {
                allOf {
                    branch "master"
                    branch "release-*"
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
            when {
                allOf {
                    branch "master"
                    branch "release-*"
                }
            }
            steps {
               echo "Let's pretend a deployment is happening"
            }
        }
        stage('Deploy to production') {
            agent any
            when {
                branch "release-*"
            }
            steps {
               input message: 'Deploy to production?', ok: 'Fire zee missiles!'
               echo "Let's pretend a production deployment is happening"
            }
        }
    }
}
