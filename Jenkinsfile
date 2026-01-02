pipeline {
    agent any
    
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World!'
                sh 'echo "Hello from shell command"'
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