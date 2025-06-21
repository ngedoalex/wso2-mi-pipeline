pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'alexsaul/wso2-mi-capp'
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/ngedoalex/wso2-mi-pipeline.git'
            }
        }

        stage('Build .car file') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${TAG} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                        echo $PASSWORD | docker login -u $USERNAME --password-stdin
                        docker push ${DOCKER_IMAGE}:${TAG}
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl set image deployment/wso2mi-deployment wso2mi=${DOCKER_IMAGE}:${TAG} --record"
            }
        }
    }
}
