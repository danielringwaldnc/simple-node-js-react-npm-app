pipeline {
    agent any
    stages {

        stage('Install newman') { 
            steps {
                sh 'sudo npm install -g newman'
            }
        }

        stage('Run newman') { 
            steps {
                withCredentials([string(credentialsId: 'postman-api-key', variable: 'POSTMAN_API_KEY')]){
                    // sh 'echo $POSTMAN_API_KEY' 
                    sh 'newman run https://api.getpostman.com/collections/30362575-ccda3095-6e43-43e1-a1d1-5608b2ae8df9?apikey={POSTMAN_API_KEY}'
                }
            }
        }
    }
}