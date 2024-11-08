pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "thrisha24/javaapp"
        DOCKER_IMAGE_VERSION = "v${BUILD_NUMBER}"
        MANIFEST_FILE = "${WORKSPACE}/k8_manifest/deployment.yaml"
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'jenkins_thirsha', url: 'https://github.com/Gowthams1999-commits/Gowtham-Javaapp-Hellow.git'  // Replace with your repo
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
                    sh 'docker build --network host -t ${DOCKER_IMAGE}:${DOCKER_IMAGE_VERSION} .'
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker_repo', passwordVariable: 'docker_pass', usernameVariable: 'docker_user')]) {
                        sh 'echo ${docker_pass} | docker login -u ${docker_user} --password-stdin'
                    }
                }
            }
        }

        stage('Image Push to Docker Hub') {
            steps {
                script {
                    sh 'docker push ${DOCKER_IMAGE}:${DOCKER_IMAGE_VERSION}'
                }
            }
        }

        stage('Update the image in deployment manifest') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'git_token', variable: 'git_token')]) {
                        // Use 'sed' to update the image version in the deployment.yaml file
                        sh """
                        sed 's|image: .*|image: ${DOCKER_IMAGE}:${DOCKER_IMAGE_VERSION}|g' ${MANIFEST_FILE} > temp_manifest.yaml
                        mv temp_manifest.yaml ${MANIFEST_FILE}
                        """

                        // Configure git and commit changes
                        sh '''
                        git config --global user.name "Gowthams1999-commits"
                        git config --global user.email "githubgowtham1999@gmail.com"
                        git add .
                        git commit -m "Update image version in deployment.yaml"
                        git push https://${git_token}@github.com/Gowthams1999-commits/Gowtham-Javaapp-Hellow.git HEAD:master
                        '''
                    }
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

