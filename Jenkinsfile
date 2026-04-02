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
        withCredentials([string(credentialsId: 'ec2-ip', variable: 'EC2_IP')]) {
            sshagent(['ec2-ssh']) {
                sh '''
                # Copy both files
                scp -o StrictHostKeyChecking=no deployment.yaml ubuntu@$EC2_IP:/home/ubuntu/
                scp -o StrictHostKeyChecking=no service.yaml ubuntu@$EC2_IP:/home/ubuntu/

                # Execute on EC2
                ssh -o StrictHostKeyChecking=no ubuntu@$EC2_IP "

                export KUBECONFIG=/home/ubuntu/.kube/config

                # Update image dynamically
                sed -i 's|image: .*|image: sridhar76/myapp:$BUILD_NUMBER|g' deployment.yaml

                # Apply deployment + service
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml

                kubectl get pods
                kubectl get svc
                "
                '''
            }
        }
    }
}
        }
    }
