
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
                bat 'if exist Dockerfile (echo Dockerfile found) else (exit /b 1)'
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
                        icacls "%SSH_KEY%" /inheritance:r
                        icacls "%SSH_KEY%" /grant:r "SYSTEM:F"

                        "C:\\Windows\\System32\\OpenSSH\\ssh.exe" -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new %SSH_USER%@%EC2_HOST% "hostname && sudo -n docker ps"
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ec2-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    bat '''
                        echo Securing SSH key...
                        icacls "%SSH_KEY%" /inheritance:r
                        icacls "%SSH_KEY%" /grant:r "SYSTEM:F"

                        echo Creating remote project directory...
                        "C:\\Windows\\System32\\OpenSSH\\ssh.exe" -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new %SSH_USER%@%EC2_HOST% "mkdir -p /home/ubuntu/Devops-CI-CD-Project"

                        echo Transferring project files to EC2...
                        "C:\\Windows\\System32\\OpenSSH\\scp.exe" -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new Dockerfile index.html %SSH_USER%@%EC2_HOST%:/home/ubuntu/Devops-CI-CD-Project/

                        echo Building Docker image on EC2...
                        "C:\\Windows\\System32\\OpenSSH\\ssh.exe" -i "%SSH_KEY%" -o StrictHostKeyChecking=accept-new %SSH_USER%@%EC2_HOST% "cd /home/ubuntu/Devops-CI-CD-Project && sudo docker build -t devops-cicd-website:%BUILD_NUMBER% . && (sudo docker rm -f devops-cicd-container || true) && sudo docker run -d --name devops-cicd-container -p 8083:80 devops-cicd-website:%BUILD_NUMBER% && for i in $(seq 1 10); do if curl -fsS http://localhost:8083; then echo "Website is responding successfully!"; exit 0; fi; echo "Waiting for Nginx..."; sleep 2; done; echo "Website health check failed"; exit 1"
                    '''
                }
            }
        }

    }
}
