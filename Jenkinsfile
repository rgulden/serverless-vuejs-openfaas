pipeline {
    agent any
    tools {nodejs "nodejs"}
    stages {
        stage('Run unit tets') {
            steps {
                sh 'echo "I should be running tests.."'
            }
        }
        stage('Build files') {
            steps {
                sh """
                    cd src/vue-app/client \
                    && npm install \
                    && npm run build
                """
                sh 'ls -l'
            }
        }
        stage('downlaod faas-cli and run code') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        cd src/
                        curl -LO https://github.com/openfaas/faas-cli/releases/download/0.12.9/faas-cli
                        chmod +x ./faas-cli

                        docker login -u ${USERNAME} -p ${PASSWORD}

                        ./faas-cli template store pull node8-express-armhf
                        ./faas-cli up -f vue-app.yml
                    """
                }
            }
        }
    }
}
