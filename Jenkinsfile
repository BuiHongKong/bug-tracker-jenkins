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
                        export PATH=$PATH:$(go env GOPATH)/bin
                        go test ./... -v -coverprofile=coverage.out 2>&1 | tee test-output.txt
                        go-junit-report -set-exit-code < test-output.txt > test-results.xml
                        go tool cover -html=coverage.out -o coverage.html
                    '''
                }
            }
            post {
                always {
                    dir('bugtracker-backend') {
                        junit 'test-results.xml'
                        publishHTML([
                            reportDir: '.',
                            reportFiles: 'coverage.html',
                            reportName: 'Backend Coverage Report',
                            keepAll: true,
                            alwaysLinkToLastBuild: true,
                            allowMissing: false
                        ])
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
