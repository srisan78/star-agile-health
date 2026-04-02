pipeline {
    agent any
    tools{
        maven 'mymaven'
    }
    stages {
        stage ('Checkout') {
            steps{
                git 'https://github.com/srisan78/star-agile-health.git'
            }
        }
        stage ('test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }
        stage ('build') {
            steps {
                echo 'Building the application...'
                sh 'mvn package'
            }
        }
        stage ('build docker image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t myapp:$BUILD_NUMBER .'
            }
        }
        stage ('push docker image') {
            steps {
                 withCredentials([string(credentialsId: 'Docker_hub', variable: 'DOCKER_HUB_PASWD')]) {
                    sh 'docker login -u sridhar76 -p $DOCKER_HUB_PASWD'
            }
             sh 'docker tag myapp:$BUILD_NUMBER sridhar76/myapp:$BUILD_NUMBER'
                sh 'docker push sridhar76/myapp:$BUILD_NUMBER'
            }
        }
    }
}