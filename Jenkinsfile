pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'npm install' 
            }
        }

        stage('Install newman') { 
            steps {
                sh 'npm install newman' 
            }
        }
    }
}