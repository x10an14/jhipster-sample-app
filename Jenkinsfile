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
        stage('Test Backend') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /home/rsandell/.m2:/root/.m2'
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
                                sh 'gulp test'
                            }
                        }
                    }
                },
                'Performance' : {
                    script {
                        node {
                            docker.image('maven:3-alpine').inside('-v /home/rsandell/.m2:/root/.m2') {
                                unstash 'ws'
                                unstash 'war'
                                sh './mvnw -B gatling:execute'
                            }
                        }
                    }
                })
            }
        }
    }
}
