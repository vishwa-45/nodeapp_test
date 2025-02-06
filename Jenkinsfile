pipeline {
    agent {
        // Use a Docker agent with the Docker CLI installed
        docker {
            image 'docker:20.10.7' // Change version if necessary
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Bind Docker socket
        }
    }
    environment {
        DOCKER_IMAGE = 'vishwa318/build-and-push'
        DOCKER_CREDENTIALS_ID = '5dd57e17-3475-451e-805d-d68d2c14c14f'
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Clone the repository
                git branch: 'main', url: 'https://github.com/vishwa-45/nodeapp_test.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                sh 'docker build -t $DOCKER_IMAGE:${BUILD_NUMBER} .'
            }
        }
        stage('Push Docker Image') {
            steps {
                // Log in to Docker Hub and push the image
                withCredentials([usernamePassword(credentialsId: "$DOCKER_CREDENTIALS_ID", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                        docker tag $DOCKER_IMAGE:${BUILD_NUMBER} $DOCKER_IMAGE:latest
                        docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }
    }
    post {
        success {
            echo "Docker image $DOCKER_IMAGE:${BUILD_NUMBER} pushed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
