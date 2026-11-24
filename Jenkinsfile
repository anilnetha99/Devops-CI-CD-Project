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

    }
}
