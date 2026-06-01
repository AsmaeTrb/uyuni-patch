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

        stage('Install Dependencies') {
            steps {
                sh 'ansible-galaxy collection install stdevel.uyuni'
            }
        }

        stage('Patch Ubuntu') {
            steps {
                withCredentials([string(credentialsId: 'vault-token', variable: 'VAULT_TOKEN')]) {
                    sh '''
                        export UYUNI_HOST=$(vault kv get -field=uyuni_server secret/uyuni/dev)
                        export UYUNI_USER=$(vault kv get -field=uyuni_user secret/uyuni/dev)
                        export UYUNI_PASSWORD=$(vault kv get -field=uyuni_password secret/uyuni/dev)
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
