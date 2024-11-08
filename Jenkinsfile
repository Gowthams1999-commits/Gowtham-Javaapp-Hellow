pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "java-demo"
        REGISTRY = "<YOUR-Docker-Registry>"  // Replace with your Docker registry, e.g., docker.io/username
        K8S_CLUSTER = "<K8S-Cluster-Name>"
        K8S_NAMESPACE = "default"  // Adjust namespace if necessary
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repository/demo.git'  // Replace with your repo
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    sh 'docker build -t $REGISTRY/$DOCKER_IMAGE:latest .'
                }
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    sh 'docker push $REGISTRY/$DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    kubectl set image deployment/java-demo-deployment java-demo=$REGISTRY/$DOCKER_IMAGE:latest --namespace=$K8S_NAMESPACE
                    kubectl rollout status deployment/java-demo-deployment --namespace=$K8S_NAMESPACE
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

