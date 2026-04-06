pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "prathamesh2019/my-app"
        EC2_IP = "13.201.32.215"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/pratham20021/my-app.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat '''
                    echo %PASS% | docker login -u %USER% --password-stdin
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-key']) {
                    bat """
                    ssh -o StrictHostKeyChecking=no ec2-user@%EC2_IP% ^
                    docker pull %DOCKER_IMAGE% ^&^& ^
                    docker stop my-app ^|^| exit 0 ^&^& ^
                    docker rm my-app ^|^| exit 0 ^&^& ^
                    docker run -d -p 8080:8080 --name my-app %DOCKER_IMAGE%
                    """
                }
            }
        }
    }
}
