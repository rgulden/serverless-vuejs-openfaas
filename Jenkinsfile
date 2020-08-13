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
        stage('copy files to pi') {
            steps {
                sshagent(credentials : ['gt-ssh']) {
                    sh 'if [ -d "/var/jenkins_home/.ssh/known_hosts" ]; then rm -Rf /var/jenkins_home/.ssh/known_hosts; fi'
                    sh """
                        ssh -o StrictHostKeyChecking=no pi@192.168.1.30 \
                        'if [ -d "src" ]; then rm -Rf src; fi'
                    """
                    sh 'rm -rf src/vue-app/client/node_modules'
                    sh 'scp -o StrictHostKeyChecking=no -r src/ pi@192.168.1.30:~/'
                }
            }
        }
        stage('ssh into pi and build/deploy function') {
            steps {
                sshagent(credentials : ['gt-ssh']) {                    
                    sh """
                        ssh -o StrictHostKeyChecking=no pi@192.168.1.30 \
                        'cd src/ \
                        && faas template store pull node8-express-armhf \
                        && cat ~/faas_pass.txt | faas-cli login --password-stdin -g 127.0.0.1:31375 \
                        && faas-cli up -f vue-app.yml'
                    """
                }
            }
        }
    }
}