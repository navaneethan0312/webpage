pipeline {
    agent any

    parameters {
        choice(
            name: 'ENV',
            choices: ['dev', 'test', 'prod'],
            description: 'Select deployment environment'
        )
    }

    environment {
        SERVER_IP   = '35.154.83.119'
        SERVER_USER = 'ec2-user'
        APP_DIR     = '/home/ubuntu' 
        SSH_KEY     = '/var/lib/jenkins/devOpslearningFeb2026.pem'
    }

    stages {

        stage('Deploy Files') {
            steps {
                sh """
                    scp -i ${SSH_KEY} -o StrictHostKeyChecking=no -r * \
                    ${SERVER_USER}@${SERVER_IP}:${APP_DIR}/${params.ENV}/
                """
            }
        }

        stage('Switch Environment') {
            steps {
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no \
                    ${SERVER_USER}@${SERVER_IP} '
                        ln -sfn ${APP_DIR}/${params.ENV} ${APP_DIR}/current
                        sudo systemctl reload nginx
                    '
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                    ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no \
                    ${SERVER_USER}@${SERVER_IP} '
                        ls -l ${APP_DIR}/current
                    '
                """
            }
        }
    }
}
