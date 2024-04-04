final Map TARGET_ENVIRONMENTS = [
    'DEV01': '30362575-b7ef0b57-a110-4319-a89b-3ef13b236989',
    'TEST01': '30362575-1c2494f7-3ff2-4c70-8658-9d299886c616',
    'QA01': '30362575-fa29cab4-c41f-4c1e-9e4c-2845c18ef95c',
    'PREPROD01': '30362575-450f5e44-bba1-4fd3-8dbe-f7f7d9e59718'
]
final Map TARGET_ENVIRONMENT_PREFIXES = [
    'DEV01': 'dev',
    'QA01': 'qa',
    'TEST01': 'test',
    'PREPROD01': 'preprod'
]

pipeline {
    agent any

    parameters {
        choice(name: 'environment', choices: ['DEV01', 'TEST01', 'QA01', 'PREPROD01'], description: 'Target environment')
        string(name: 'delay', defaultValue: '2000', description: 'Delay between REST request')
    }

    environment {
        COLLECTION_ID = "30362575-ccda3095-6e43-43e1-a1d1-5608b2ae8df9"
        DELAY = "${params.delay}"
        PUBLIC_CREDENTIAL_ID = "${TARGET_ENVIRONMENT_PREFIXES[params.environment]}" + '-jwt-pub-key'
        PRIVATE_CREDENTIAL_ID = "${TARGET_ENVIRONMENT_PREFIXES[params.environment]}" + '-jwt-priv-key'
        ENVIRONMENT_TEST = "${TARGET_ENVIRONMENTS[params.environment]}"
    }

    stages {

        stage('Print Parameters') {
            steps {
                script {
                    // Iterating over each parameter
                    params.each { paramKey, paramValue ->
                        echo "Parameter Name: ${paramKey}, Value: ${paramValue}"
                    }
                }

                sh "echo $ENVIRONMENT"
            }
        }

        stage('Install newman') { 
            steps {
                sh 'npm install newman'
            }
        }

        stage('Run newman') { 
            steps {
                withCredentials([string(credentialsId: 'postman-api-key', variable: 'POSTMAN_API_KEY'),
                                string(credentialsId: 'jwt-pub-key', variable: 'JWT_PUBLIC_KEY'),
                                string(credentialsId: 'jwt-priv-key', variable: 'JWT_PRIVATE_KEY')]) {
                    script {
                        def delay = "${params.delay}"
                        if (params.environment == 'DEV01') {
                        withEnv(['environment_id=30362575-b7ef0b57-a110-4319-a89b-3ef13b236989']) {
                        sh '''
                            PROCESSED_PUBLIC=$(echo "$JWT_PUBLIC_KEY" | tr -d '\n')
                            PROCESSED_PRIVATE=$(echo "$JWT_PRIVATE_KEY" | tr -d '\n')
                            npx newman run https://api.getpostman.com/collections/${COLLECTION_ID}?apikey=${POSTMAN_API_KEY} --environment https://api.getpostman.com/environments/${environment_id}?apikey=${POSTMAN_API_KEY} --env-var jwt_pub_key="${PROCESSED_PUBLIC}" --env-var jwt_priv_key="${PROCESSED_PRIVATE}" --delay-request ${delay} --insecure
                        '''
                        } }
                    }
                }
            }
        }
    }
}