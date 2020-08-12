pipeline {
    agent any
    stages {
        stage('Run unit tets') {
            steps {
                sh 'echo "I should be running tests.."'
            }
        }
        stage('Build files') {
            steps {
                sh 'cd src/vue-app/client'
                
                sh """
                    curl https://nodejs.org/dist/v12.18.3/node-v12.18.3.tar.gz | tar xz --strip-components=1

                    export NODEJS_HOME=$PWD/node-v12.18.3/bin
                    export PATH=$NODEJS_HOME:$PATH

                    node -v
                    npm version
                """
                
                sh 'npm install'
                sh 'npm run build'
                sh 'cd ../../..'
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
                        && faas-cli up -f daily-status.yml'
                    """
                }
            }
        }
        stage('Check k3 cluster pods') {
            steps {
                withCredentials([file(credentialsId: 'config', variable: 'config')]) {
                    sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
                    sh 'chmod +x ./kubectl'
                    sh './kubectl version --client'
                    
                    sh "cat \$config >> config"
                    sh "export KUBECONFIG=config"
                    
                    sh "./kubectl get pods -n openfaas-fn"
                }
            }
        }
    }
}