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
        KEY = '/home/navaneethan/devOpslearningFeb2026.pem'
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

                # remove old files
                sudo rm -rf /var/www/devops-site/*

                # copy latest code from GitHub clone location (via Jenkins workspace)
                sudo cp -r /var/lib/jenkins/workspace/* /var/www/devops-site/

                # fix permission
                sudo chmod -R 755 /var/www/devops-site

                # restart nginx
                sudo systemctl restart nginx

                EOF
                '''
            }
        }
    }
}
