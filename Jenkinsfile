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
                withCredentials([usernamePassword(credentialsId: 'ssh-password-root-142.93.222.67', 
                                                usernameVariable: 'SSH_USER', 
                                                passwordVariable: 'SSH_PASS')]) {
                    sh """
                    sshpass -p "$SSH_PASS" ssh -o StrictHostKeyChecking=no ${SSH_USER}@${DEPLOY_SERVER} 'mkdir -p ${DEPLOY_PATH}'
                    sshpass -p "$SSH_PASS" scp target/*.jar ${SSH_USER}@${DEPLOY_SERVER}:${DEPLOY_PATH}/app.jar
                    sshpass -p "$SSH_PASS" ssh -o StrictHostKeyChecking=no ${SSH_USER}@${DEPLOY_SERVER} 'nohup java -jar ${DEPLOY_PATH}/app.jar > ${DEPLOY_PATH}/app.log 2>&1 &'
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
