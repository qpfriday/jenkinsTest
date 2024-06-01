pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')
        DOCKERHUB_REPO = 'qpfriday/chandol'
        DOCKER_IMAGE_TAG = "${DOCKERHUB_REPO}:${BUILD_NUMBER}"
    }

    stages {
        stage('Git Clone') {
            steps {
                script {
                    // Git clone
                    sh 'git clone -b master https://github.com/qpfriday/jenkinsTest.git'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Set execute permissions for gradlew
                    sh 'chmod +x ./jenkinsTest/gradlew'
                    // Build
                    sh './jenkinsTest/gradlew clean build'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Docker image build
                    sh "docker build -t ${DOCKER_IMAGE_TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        // Docker image push
                        sh "docker push ${DOCKER_IMAGE_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Docker image removal
            sh "docker rmi ${DOCKER_IMAGE_TAG}"
        }
        success {
            // Success message
            echo 'Docker image pushed to Docker Hub successfully!'
        }
        failure {
            // Failure message
            echo 'Build or push failed.'
        }
    }
}
