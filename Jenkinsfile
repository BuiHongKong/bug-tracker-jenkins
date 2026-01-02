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
                    sh 'go test ./... -v'
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
            echo 'Pipeline completed!'
        }
    }
}
