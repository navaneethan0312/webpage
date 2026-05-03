pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'test', 'production'],
            description: 'எந்த environment-ல deploy பண்ணணும்?'
        )
    }

    environment {
        SERVER_IP = '35.154.83.119'
        SERVER_USER = 'ec2user'
        DEPLOY_PATH = "/var/www/${params.ENVIRONMENT}"
        REPO_URL = 'https://github.com/navaneethan0312/DevOps-Training-.git'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "GitHub-ல இருந்து code எடுக்கிறோம்..."
                git url: "${REPO_URL}", branch: 'main'
            }
        }

        stage('Deploy') {
            steps {
                echo "${params.ENVIRONMENT} environment-ல deploy பண்றோம்..."
                sshagent(['ec2-deploy-key']) {
                    sh """
                        # Deploy folder clear பண்ணி புது files copy பண்ணு
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} \
                            "rm -rf ${DEPLOY_PATH}/* && mkdir -p ${DEPLOY_PATH}"
                        
                        scp -o StrictHostKeyChecking=no -r ./* \
                            ${SERVER_USER}@${SERVER_IP}:${DEPLOY_PATH}/
                    """
                }
            }
        }

        stage('Update Nginx') {
            steps {
                echo "Nginx-ஐ ${params.ENVIRONMENT} folder-க்கு point பண்றோம்..."
                sshagent(['ec2-deploy-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} \
                        "sudo sed -i 's|root /var/www/.*;|root /var/www/${params.ENVIRONMENT};|' \
                        /etc/nginx/sites-available/devops-learning && \
                        sudo systemctl reload nginx"
                    """
                }
            }
        }

        stage('Done') {
            steps {
                echo "✅ Deploy complete! http://devops-learning.bontonsoftwares.com பாரு"
            }
        }
    }
}
