pipeline {
    agent any
    environment {
        
        DOCKER_CREDS = credentials('Docker_hub')
    }
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
                sh 'echo $DOCKER_CREDS_PSW | docker login -u $DOCKER_CREDS_USR --password-stdin'
             sh 'docker tag myapp:$BUILD_NUMBER sridhar76/myapp:$BUILD_NUMBER'
                sh 'docker push sridhar76/myapp:$BUILD_NUMBER'
            }
        }
        stage ('deploy to kubernetes') {
            stage('Deploy') {
            steps {
                sh '''
                export KUBECONFIG=$HOME/.kube/config
                kubectl get nodes
                kubectl apply -f deployment.yaml
                '''
            }
        }
        }
    }
}