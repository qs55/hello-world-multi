pipeline {
    agent any

    environment {
        // Updated to reflect the new Docker image name
        DOCKER_IMAGE = "hello-world-multi"
        // New container name
        CONTAINER_NAME = "hello-world-container"
        // Versioning (you can change this to use another versioning scheme if you want)
        VERSION = "v1.0.${BUILD_NUMBER}"  // Use Jenkins build number as version
        BRANCH_TAG = "${env.BRANCH_NAME}"  // Git branch name (e.g., 'master', 'develop')
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image and tag it with the branch name and version
                    def imageTag = "${DOCKER_IMAGE}:${BRANCH_TAG}-${VERSION}"
                    docker.build(imageTag)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: '') {
                    script {
                        // Tag the image with branch name and version before pushing
                        def imageTag = "${DOCKER_IMAGE}:${BRANCH_TAG}-${VERSION}"
                        // Push the image to Docker Hub with the new tag
                        docker.image(imageTag).push()
                    }
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    // Pull the latest image from Docker Hub based on branch and version
                    def imageTag = "${DOCKER_IMAGE}:${BRANCH_TAG}-${VERSION}"
                    sh "docker pull ${imageTag}"

                    // Stop and remove any existing container with the same name (optional)
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"

                    // Run the Docker container with the newly tagged image
                    sh "docker run -d --name ${CONTAINER_NAME} -p 5000:5000 ${imageTag}"
                }
            }
        }
    }

    post {
        always {
            // Clean up the workspace after the pipeline completes
            cleanWs()
        }
    }
}
