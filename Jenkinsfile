pipeline {
    agent any

    environment {
        // Docker image name (with your Docker Hub username)
        DOCKER_IMAGE = "qaiser55/hello-world-multi"
        // Container name for local deployment
        CONTAINER_NAME = "hello-world-container-7000"
        // Versioning based on Jenkins build number
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
                // Securely login to Docker Hub using stored Jenkins credentials
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        // Log in to Docker Hub using the access token (password via stdin for security)
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'

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
                    sh "docker run -d --name ${CONTAINER_NAME} -p 7000:5000 ${imageTag}"
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
