pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS = credentials('docker-registry-credentials')  
        FRONTEND_IMAGE = "omarz1/final-project:frontend.${BUILD_NUMBER}" 
        BACKEND_IMAGE = "omarz1/final-project:backend.${BUILD_NUMBER}"  
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning the repository...'
                git branch: 'main', url: 'https://github.com/omar-k1/DevOps-proejct.git' 
            }
        }

        stage('Build and Push Docker Images') {
            parallel {
                stage('Build & Push Backend') {
                    steps {
                        dir('backend-flask') {
                            sh """
                                echo "Building Backend Docker Image: $BACKEND_IMAGE"
                                docker build -t $BACKEND_IMAGE .
                                echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                                docker push $BACKEND_IMAGE
                            """
                        }
                    }
                }

                stage('Build & Push Frontend') {
                    steps {
                        dir('frontend-html') {
                            sh """
                                echo "Building Frontend Docker Image: $FRONTEND_IMAGE"
                                docker build -t $FRONTEND_IMAGE .
                                echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin
                                docker push $FRONTEND_IMAGE
                            """
                        }
                    }
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                sh """
                    sed -i "s|image: .*frontend.*|image: $FRONTEND_IMAGE|g" k8s-manifests/frontend-deployment.yml
                    sed -i "s|image: .*backend.*|image: $BACKEND_IMAGE|g" k8s-manifests/backend-deployment.yml
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Applying Kubernetes manifests...'
                sh """
                    kubectl apply -f k8s-manifests/backend-configmap.yml
                    kubectl apply -f k8s-manifests/backend-pv.yml
                    kubectl apply -f k8s-manifests/backend-pvc.yml
                    kubectl apply -f k8s-manifests/backend-deployment.yml
                    kubectl apply -f k8s-manifests/backend-service.yml
                    kubectl apply -f k8s-manifests/mysql-configmap.yml
                    kubectl apply -f k8s-manifests/mysql-secret.yml
                    kubectl apply -f k8s-manifests/mysql-pv.yml
                    kubectl apply -f k8s-manifests/mysql-pvc.yml
                    kubectl apply -f k8s-manifests/mysql-secret.yml
                    kubectl apply -f k8s-manifests/mysql-deployment.yml
                    kubectl apply -f k8s-manifests/mysql-service.yml
                    kubectl apply -f k8s-manifests/frontend-deployment.yml
                    kubectl apply -f k8s-manifests/frontend-service.yml
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs for details.'
        }
    }
}

