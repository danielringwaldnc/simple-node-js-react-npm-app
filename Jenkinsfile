final String HTML_TEST_RESULTS_PATH = '${WORKSPACE}/build/result/test-results.html'

final Map TARGET_ENVIRONMENTS = [
    'DEV01': '28909170-5bf26702-6d8d-426b-b873-007f1afb6d1e',
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
        stage('Install newman') { 
            steps {
                sh 'npm install newman'
                sh 'npm install newman-reporter-htmlextra'
                sh 'echo $WORKSPACE'
            }
        }

        stage('Run newman') { 
            steps {
                script {
                    
                withCredentials([string(credentialsId: 'postman-api-key', variable: 'POSTMAN_API_KEY'),
                                 string(credentialsId: env.PUBLIC_CREDENTIAL_ID, variable: 'JWT_PUBLIC_KEY'),
                                 string(credentialsId: env.PRIVATE_CREDENTIAL_ID, variable: 'JWT_PRIVATE_KEY')]) {
                        
                        def delay = "${params.delay}"
                        def html_result_path = "${HTML_TEST_RESULTS_PATH}"
                        
                        withEnv(["environment_id=${TARGET_ENVIRONMENTS[params.environment]}"]) {
                                sh '''
                                    PROCESSED_PUBLIC=$(echo "$JWT_PUBLIC_KEY" | tr -d '\n')
                                    PROCESSED_PRIVATE=$(echo "$JWT_PRIVATE_KEY" | tr -d '\n')
                                    npx newman run https://api.getpostman.com/collections/${COLLECTION_ID}?apikey=${POSTMAN_API_KEY} --environment https://api.getpostman.com/environments/${environment_id}?apikey=${POSTMAN_API_KEY} --env-var jwt_pub_key="${PROCESSED_PUBLIC}" --env-var jwt_priv_key="${PROCESSED_PRIVATE}" --reporter-htmlextra-export ${html_result_path} --delay-request ${delay} --insecure
                                '''
                        }
                    }
                }
            }
        }
    }
}