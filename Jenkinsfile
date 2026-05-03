pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'test', 'production'],
            description: 'Select the environment to deploy'
        )
        string(
    name: 'PEM_PATH',
    defaultValue: '/var/lib/jenkins/devOpslearningFeb2026.pem',
    description: 'Full path to your PEM key file on this machine'
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
        sh """
            chmod 600 ${params.PEM_PATH}
            
            # Server-ல folder clean பண்ணு
            ssh -o StrictHostKeyChecking=no -i ${params.PEM_PATH} ${SERVER_USER}@${SERVER_IP} \
                "rm -rf ${DEPLOY_PATH}/* && mkdir -p ${DEPLOY_PATH}"
            
            # HTML files copy பண்ணு
            scp -o StrictHostKeyChecking=no -i ${params.PEM_PATH} -r ./HTML/* \
                ${SERVER_USER}@${SERVER_IP}:${DEPLOY_PATH}/
            
            # CSS folder copy பண்ணு
            scp -o StrictHostKeyChecking=no -i ${params.PEM_PATH} -r ./CSS \
                ${SERVER_USER}@${SERVER_IP}:${DEPLOY_PATH}/
            
            # Images folder copy பண்ணு
            scp -o StrictHostKeyChecking=no -i ${params.PEM_PATH} -r ./Images \
                ${SERVER_USER}@${SERVER_IP}:${DEPLOY_PATH}/
        """
    }
}

        stage('Update Nginx') {
            steps {
                echo "Pointing Nginx to ${params.ENVIRONMENT} folder..."
                sh """
                    chmod 600 ${params.PEM_PATH}
                    ssh -o StrictHostKeyChecking=no -i ${params.PEM_PATH} ${SERVER_USER}@${SERVER_IP} \
                    "sudo sed -i 's|root /var/www/.*;|root /var/www/${params.ENVIRONMENT};|' \
                    /etc/nginx/sites-available/devops-learning && \
                    sudo systemctl reload nginx"
                """
            }
        }

        stage('Done') {
            steps {
                echo "Deploy complete! Visit: http://devops-learning.bontonsoftwares.com"
            }
        }
    }
}
