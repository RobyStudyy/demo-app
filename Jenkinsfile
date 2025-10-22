pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKERHUB_REPO = 'roby20/demo-app'
        KUBECONFIG = '/home/jenkins/.kube/config'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/RobyStudyy/demo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKERHUB_REPO:latest .'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKERHUB_REPO:latest
                    '''
                }
            }
        }

        stage('Deploy to Minikube using Helm') {
            steps {
                script {
                    sh '''
                    helm upgrade --install demo-app helm/demo-app \
                      --set image.repository=$DOCKERHUB_REPO \
                      --set image.tag=latest
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
            }
        }
    }
}
