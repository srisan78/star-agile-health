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
    
           stage('Deploy to Kubernetes') {
    steps {
        sshagent(['ec2-ssh']) {
            sh '''
            # Copy deployment.yaml from Jenkins to EC2
            scp -o StrictHostKeyChecking=no deployment.yaml ubuntu@18.215.178.68:/home/ubuntu/

            # Run deployment on EC2
            ssh -o StrictHostKeyChecking=no ubuntu@18.215.178.68 "

            # Update image dynamically
            sed -i 's|image: .*|image: sridhar76/myapp:$BUILD_NUMBER|g' deployment.yaml

            kubectl get nodes
            kubectl apply -f deployment.yaml
            kubectl apply -f service.yaml

            "
            '''
        }
    }
}
        }
    }
