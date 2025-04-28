pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "qaiser55/hello-flask-app"
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: '') {
                    script {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }

        stage('Deploy to Local Docker') {
            steps {
                script {
                    // Pull the latest image from Docker Hub
                    sh "docker pull ${DOCKER_IMAGE}"

                    // Stop and remove any existing container with the same name (optional)
                    sh "docker stop my-flask-app || true"
                    sh "docker rm my-flask-app || true"

                    // Run the Docker container with the new image
                    sh "docker run -d --name my-flask-app -p 5000:5000 ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}