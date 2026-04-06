pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-dockerhub-username/my-app"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/your-username/my-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                ssh ec2-user@<EC2-IP> '
                docker pull your-dockerhub-username/my-app
                docker stop my-app || true
                docker rm my-app || true
                docker run -d -p 8080:8080 --name my-app your-dockerhub-username/my-app
                '
                '''
            }
        }
    }
}
