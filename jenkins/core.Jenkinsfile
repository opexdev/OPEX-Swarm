pipeline {
    agent {
        label "opex-dev"
    }

    stages {
        stage('Deploy Swarm') {
            environment {
                TAG = 'dev'
                PREFERENCES = "preferences.yml"
                PANEL_PASS = credentials("opex-dev-v-panel-secret")
                BACKEND_USER = credentials("opex-dev-v-backend-secret")
                SMTP_PASS = credentials("opex-dev-smtp-secret")
                DB_USER = 'opex'
                DB_PASS = credentials("opex-dev-db-secret")
                DB_READ_ONLY_USER = 'opex_reader'
                DB_READ_ONLY_PASS = credentials("opex-dev-db-reader-secret")
                KEYCLOAK_ADMIN_USERNAME = credentials("opex-dev-keycloak-admin-username")
                KEYCLOAK_ADMIN_PASSWORD = credentials("opex-dev-keycloak-admin-password")
                OPEX_ADMIN_KEYCLOAK_CLIENT_SECRET = credentials("opex-dev-opex-admin-keycloak-client-secret")
                //VANDAR_API_KEY = credentials("vandar-api-key")
                //FILEBEAT_API_KEY = credentials("filebeat-api-key")
            }
            steps {
                withCredentials([
                    file(credentialsId: 'private.pem', variable: 'PRIVATE'),
                    file(credentialsId: 'opex.dev.crt', variable: 'PUBLIC')
                ]) {
                    sh 'cp -f $PRIVATE ./private.pem'
                    sh 'cp -f $PUBLIC ./opex.dev.crt'
                }
                configFileProvider([
                    configFile(fileId: 'preferences-dev.yml', variable: 'PREFERENCES_YML'),
                    configFile(fileId: 'whitelist.txt', variable: 'WHITELIST_TXT')
                ]) {
                    sh 'cp -f $PREFERENCES_YML ./preferences.yml'
                    sh 'cp -f $WHITELIST_TXT ./whitelist.txt'
                }
                sh 'docker stack deploy \
                        -c docker-stack.yml \
                        -c docker-stack.dev.yml \
                           opex-dev'
            }
        }
    }

    post {
        always {
            echo 'One way or another, I have finished'
        }
        success {
            echo ':)'
        }
        unstable {
            echo ':/'
        }
        failure {
            echo ':('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
