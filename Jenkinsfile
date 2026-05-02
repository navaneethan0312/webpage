pipeline {
    agent any

    parameters {
        choice(
            name: 'ENV',
            choices: ['dev', 'test', 'prod'],
            description: 'Choose environment'
        )
    }

    environment {
        SERVER = '35.154.83.119'
        USER = 'ec2-user'
        KEY = '/home/jenkins/devOpslearningFeb2026.pem'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/navaneethan0312/DevOps-Training-.git'
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                ssh -i $KEY -o StrictHostKeyChecking=no $USER@$SERVER << 'EOF'
                sudo rm -rf /var/www/devops-site/*
                sudo cp -r /var/lib/jenkins/workspace/* /var/www/devops-site/
                sudo systemctl restart nginx
                EOF
                '''
            }
        }

    }
}
