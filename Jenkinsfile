pipeline {
    agent any

    stages {

        stage('Execute unit tests') {
            parallel {
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
                                rm -rf reports
                                mkdir -p reports
                                mv coverage reports/
                            '''
                        }
                    }
                    post {
                        always {
                            dir('bugtracker-frontend') {
                                junit 'test-results.xml'
                                publishHTML([
                                    reportDir: 'reports/coverage',
                                    reportFiles: 'index.html',
                                    reportName: 'Frontend Coverage Report',
                                    keepAll: true,
                                    alwaysLinkToLastBuild: true,
                                    allowMissing: false
                                ])
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'docker:latest'
                    reuseNode true
                    args '-v /var/run/docker.sock:/var/run/docker.sock -u 0'
                }
            }
            steps {
                echo 'Deploying...'
                sh "docker compose up --build -d"
            }
        }

        stage('API Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright/playwright:v1.50.0-jammy'
                    reuseNode true
                    args '-u 0 --network=host'
                }
            }
            steps {
                dir('tests-api') {
                    sh '''
                        npx wait-port http://localhost:8081/api/health -t 30000
                        npm ci
                        npx playwright test
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }

}