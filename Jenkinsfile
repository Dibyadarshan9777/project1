pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')  // Add Docker Hub credentials in Jenkins
        DOCKER_IMAGE = 'dibyadarshandevops/project1:v1.0'
        KUBECONFIG_PATH = '/var/jenkins_home/.kube/config'  // Path to kubeconfig inside Jenkins container
        HELM_RELEASE = 'project1-release'  // Helm release name
        HELM_CHART_PATH = '/home/dbeher735/project/1-html5up-forty/project1'  // Path to your Helm chart
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

        stage('Deploy to Kubernetes with Helm') {
            steps {
                script {
                    echo 'Deploying to Kubernetes with Helm...'
                    sh '''
                        helm upgrade --install $HELM_RELEASE $HELM_CHART_PATH \
                        --set image.repository=$DOCKER_IMAGE \
                        --namespace dbspe
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning up...'
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

