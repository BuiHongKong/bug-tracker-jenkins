pipeline {
    agent any

    stages {
        stage('Unit test backend') {
            agent {
                docker {
                    image 'golang:1.21'
                    reuseNode true
                }
            }
            environment {
                GOCACHE = '/tmp/go-build-cache'
            }
            steps {
                echo 'Running unit tests for backend...'
                dir('bugtracker-backend') {
                    sh '''
                        go install github.com/jstemmer/go-junit-report/v2@latest
                        go test ./... -v 2>&1 | go-junit-report > test-results.xml
                    '''
                }
            post {
                always {
                    dir('bugtracker-backend') {
                        junit 'test-results.xml'
                    }
                }
            }
            }
        }

        stage('Unit test frontend') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Running unit tests for frontend...'
                dir('bugtracker-frontend') {
                    sh '''
                        npm ci
                        npm test
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }

    post {
        always {
           dir('bugtracker-frontend') {
            junit 'test-results.xml'
           }
        }
    }
}
