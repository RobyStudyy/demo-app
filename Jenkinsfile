pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = 'roby20/demo-app'
        KUBECONFIG = '/home/jenkins/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/RobyStudyy/demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_REPO:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "Logging in to DockerHub..."
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKERHUB_REPO:latest
                    '''
                }
            }
        }

        stage('Deploy to Minikube using Helm') {
            steps {
                sh '''
                    helm upgrade --install demo-app helm/demo-app \
                      --set image.repository=$DOCKERHUB_REPO \
                      --set image.tag=latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
            }
        }
    }
}
