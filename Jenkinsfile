pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'test', 'production'],
            description: 'Select the environment to deploy'
        )
    }

    environment {
        SERVER_IP = '35.154.83.119'
        SERVER_USER = 'ec2-user'
        DEPLOY_PATH = "/var/www/${params.ENVIRONMENT}"
        REPO_URL = 'https://github.com/navaneethan0312/DevOps-Training-.git'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Fetching code from GitHub..."
                git url: "${REPO_URL}", branch: 'master'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying to ${params.ENVIRONMENT} environment..."
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-deploy-key', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        chmod 600 $SSH_KEY
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@${SERVER_IP} \
                            "rm -rf ${DEPLOY_PATH}/* && mkdir -p ${DEPLOY_PATH}"
                        scp -o StrictHostKeyChecking=no -i $SSH_KEY -r ./HTML/* \
                            ec2-user@${SERVER_IP}:${DEPLOY_PATH}/
                    """
                }
            }
        }

        stage('Update Nginx') {
            steps {
                echo "Pointing Nginx to ${params.ENVIRONMENT} folder..."
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2-deploy-key', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        chmod 600 $SSH_KEY
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ec2-user@${SERVER_IP} \
                        "sudo sed -i 's|root /var/www/.*;|root /var/www/${params.ENVIRONMENT};|' \
                        /etc/nginx/sites-available/devops-learning && \
                        sudo systemctl reload nginx"
                    """
                }
            }
        }

        stage('Done') {
            steps {
                echo "Deploy complete! Visit: http://devops-learning.bontonsoftwares.com"
            }
        }
    }
}
