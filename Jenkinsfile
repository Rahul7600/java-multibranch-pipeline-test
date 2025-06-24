pipeline {
    agent any
    environment {
        DEPLOY_SERVER = "142.93.222.67"   // App server IP
        DEPLOY_USER = "root"              // SSH user
        DEPLOY_PATH = "/var/www/java-apps" // Deployment directory
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key-root-142.93.222.67',
                                                  keyFileVariable: 'SSH_KEY')]) {
                    sh """
                    ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DEPLOY_SERVER} '
                        mkdir -p ${DEPLOY_PATH}
                        pkill -f "${DEPLOY_PATH}/app.jar" || true
                        nohup java -jar ${DEPLOY_PATH}/app.jar > ${DEPLOY_PATH}/app.log 2>&1 &
                        sleep 5
                        ps -ef | grep "java -jar" || echo "Application did not start!"
                        tail -n 20 ${DEPLOY_PATH}/app.log
                    '
                    """
                }
            }
        }
    }
    post {
        always {
            echo "Pipeline completed for branch: ${env.BRANCH_NAME}"
        }
    }
}
