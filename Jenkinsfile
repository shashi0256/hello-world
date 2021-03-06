// Initialize a LinkedHashMap / object to share between stages
def pipelineContext = [:]

pipeline {
    agent any

    environment {
        DOCKER_IMAGE_TAG = "my-app:build-${env.BUILD_ID}"
    }

    stages {
        stage('Configure') {
            steps {
                echo 'Create parameters file'
            }
        }
        stage('Build') {
            steps {
                echo "Build docker image"
                script {
                    dockerImage = docker.build("${env.DOCKER_IMAGE_TAG}",  '-f ./Dockerfile .')
                    pipelineContext.dockerImage = dockerImage
                }
            }
        }
        stage('Run') {
            steps {
                echo "Run docker image"
                script {
                    pipelineContext.dockerContainer = pipelineContext.dockerImage.run()
                }
            }
        }
        stage('Test') {
            steps {
                echo "Testing the app"
            }
        }
        stage('Push') {
            steps {
                echo "Pushing the Docker image to the registry"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying the Docker image"
            }
        }
        stage('Verify') {
            parallel {
                stage('Verify home') {
                    agent any
                    steps {
                        echo "HTTP request to verify home"
                    }
                }
                stage('Verify health check') {
                    agent any
                    steps {
                        echo "HTTP request to verify application health check"
                    }
                }
                stage('Verify regression tests') {
                    agent any
                    steps {
                        echo "Running regression test suite"
                    }
                }
            }
        }
    }
    post {
        always {
            echo "Stop Docker image"
            script {
                if (pipelineContext && pipelineContext.dockerContainer) {
                    pipelineContext.dockerContainer.stop()
                }
            }
        }
    }
}
