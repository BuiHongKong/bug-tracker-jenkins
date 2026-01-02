pipeline {
    agent any
    
    stages {
        stage('Unit test backend') {
            agent {
                docker {
                    image 'golang:1.21'
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
        
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
        }
    }
}