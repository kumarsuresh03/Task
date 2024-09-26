pipeline {
    agent any
    environment {
        dockerRegistry = "https://index.docker.io/v1/"
        dockerCreds = credentials('dockerhub-credentials')  // Your Docker Hub credentials
        mongodbImage = 'mern-mongodb'
        nodeImage = 'mern-node'
    }
    stages {
        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry(dockerRegistry, 'dockerhub-credentials') {
                        echo "Logged in to Docker Hub"
                    }
                }
            }
        }
        stage('Pull MongoDB Image') {
            steps {
                script {
                    echo "Pulling MongoDB image from Docker Hub"
                    bat "docker pull mongo:latest"  // You can also specify a version like mongo:4.4
                }
            }
        }
        stage('Build MongoDB') {
            steps {
                script {
                    def mongodbPath = "MongoDB"
                    def dockerfileMongo = "MongoDB/Dockerfile.mongo"
                    if (fileExists(mongodbPath)) {
                        bat "docker build -f ${dockerfileMongo} -t ${mongodbImage}:latest ${mongodbPath}"
                        bat "docker tag ${mongodbImage}:latest sureshnangina/mern-mongodb:latest"
                    } else {
                        error "MongoDB directory ${mongodbPath} not found"
                    }
                }
            }
        }
        stage('Build Node.js') {
            steps {
                script {
                    def nodePath = "Node-App"
                    def dockerfileNode = "Node-App/Dockerfile"
                    if (fileExists(nodePath)) {
                        bat "docker build -f ${dockerfileNode} -t ${nodeImage}:latest ${nodePath}"
                        bat "docker tag ${nodeImage}:latest sureshnangina/mern-node:latest"
                    } else {
                        error "Node.js directory ${nodePath} not found"
                    }
                }
            }
        }
        stage('Push MongoDB') {
            steps {
                script {
                    docker.withRegistry(dockerRegistry, 'dockerhub-credentials') {
                        echo "Pushing MongoDB image to Docker Hub"
                        bat "docker push sureshnangina/mern-mongodb:latest"
                    }
                }
            }
        }
        stage('Push Node.js') {
            steps {
                script {
                    docker.withRegistry(dockerRegistry, 'dockerhub-credentials') {
                        echo "Pushing Node.js image to Docker Hub"
                        bat "docker push sureshnangina/mern-node:latest"
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
