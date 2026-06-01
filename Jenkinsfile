pipeline {
    agent any

    environment {
        VAULT_ADDR = 'http://127.0.0.1:8200'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "✅ Code récupéré depuis Git"
            }
        }

        stage('Patch Ubuntu') {
            steps {
                withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
                    sh '''
                        ansible-playbook -i uyuni.yml patch_management.yml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Patches appliqués avec succès !'
        }
        failure {
            echo '❌ Erreur pendant le patch management !'
        }
    }
}
