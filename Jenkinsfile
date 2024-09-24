pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')  // Add Docker Hub credentials in Jenkins
        DOCKER_IMAGE = 'dibyadarshandevops/project1:v1.0'
        KUBECONFIG_PATH = '/var/jenkins_home/.kube/config'  // Correct path to kubeconfig in the container
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning repository...'
                git 'https://github.com/Dibyadarshan9777/project1.git'  // Update with your repo URL
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Running tests...'
                    // Example of running a basic test, replace with your own test scripts
                    sh 'docker run --rm $DOCKER_IMAGE /bin/sh -c "echo Test successful!"'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    echo 'Logging into Docker Hub...'
                    sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying to Kubernetes...'
                    // Use the correct kubeconfig path inside the Jenkins container
                    sh 'export KUBECONFIG=$KUBECONFIG_PATH'
                    
                    // Example using kubectl to apply a Kubernetes manifest file
                    sh '''
                        kubectl apply -f k8s/deployment.yaml --namespace dbspe
                    '''
                    
                    // Alternatively, if you're using Helm, uncomment the following lines
                    /*
                    sh '''
                        helm upgrade --install project1 ./helm-chart \
                        --set image.repository=$DOCKER_IMAGE \
                        --namespace dbspe
                    '''
                    */
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning up Docker images...'
                sh 'docker rmi $DOCKER_IMAGE || true'
            }
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

