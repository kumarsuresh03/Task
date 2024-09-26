pipeline {
    agent any
    environment {
        dockerRegistry = "https://index.docker.io/v1/"
        dockerCreds = credentials('dockerhub-credentials')
        mongodbImage = 'mern-mongodb'
        nodeImage = 'mern-node'
    }
    stages {
        stage('Build MongoDB') {
            steps {
                script {
                    def mongodbPath = "MongoDB"
                    if (fileExists(mongodbPath)) {
                        bat "docker build -t ${mongodbImage}:latest ${mongodbPath}"
                        bat "docker tag ${mongodbImage} sureshnangina/mern-mongodb:mern-mongodb"
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
                    if (fileExists(nodePath)) {
                        bat "docker build -t ${nodeImage}:latest ${nodePath}"
                        bat "docker tag ${nodeImage} sureshnangina/mern-node:mern-node"
                    } else {
                        error "Node.js directory ${nodePath} not found"
                    }
                }
            }
        }
        stage('Push MongoDB') {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1/", 'dockerhub-credentials') {
                        echo "Pushing MongoDB image to Docker Hub"
                        bat "docker push sureshnangina/mern-mongodb:${mongodbImage}"
                    }
                }
            }
        }
        stage('Push Node.js') {
            steps {
                script {
                    docker.withRegistry(dockerRegistry, 'dockerhub-credentials') {
                        echo "Pushing Node.js image to Docker Hub"
                        bat "docker push sureshnangina/mern-node:${nodeImage}"
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
