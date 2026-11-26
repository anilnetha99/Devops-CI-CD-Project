
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking project source code'
                git branch: 'main',
                    url: 'https://github.com/anilnetha99/Devops-CI-CD-Project.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Build Started Successfully!'
                bat 'dir'
                bat 'type index.html'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing project files'
                bat 'if exist index.html (echo HTML file found) else (exit /b 1)'
            }
        }

        stage('EC2 SSH Connection Test') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ec2-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                        echo Securing temporary SSH key permissions...
                        icacls "%SSH_KEY%" /inheritance:r
                        icacls "%SSH_KEY%" /grant:r "SYSTEM:F"

                        echo Connecting to AWS EC2...
                        "C:\\Windows\\System32\\OpenSSH\\ssh.exe" -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new %SSH_USER%@%EC2_HOST% "hostname && sudo -n docker ps"
                    '''
                }
            }
        }

    }
}
