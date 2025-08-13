pipeline {
    agent any

    environment {
        IMAGE_NAME = "hello"  // local image name
        ECR_REPO  = "992382545251.dkr.ecr.us-east-1.amazonaws.com/anna" // ECR repo
        REGION    = "us-east-1"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Test Docker Image') {
            steps {
                // Optional: run your Flask app or unit tests inside the container
                // sh "docker run --rm ${IMAGE_NAME} pytest"
		echo "Skipping tests for now"
            }
        }

        stage('Login, Tag & Push Docker Image') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: '992382545251'  // <-- your Jenkins AWS credentials ID
                ]]) {
                    sh """
                        echo "Logging in to ECR..."
                        aws ecr get-login-password --region ${REGION} | \
                        docker login --username AWS --password-stdin ${ECR_REPO}

                        echo "Tagging Docker image..."
                        docker tag ${IMAGE_NAME}:latest ${ECR_REPO}:latest

                        echo "Pushing Docker image..."
                        docker push ${ECR_REPO}:latest
                    """
                }
            }
        }
    }
}
