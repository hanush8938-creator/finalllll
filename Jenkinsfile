
pipeline {
    agent any

    environment {
        IMAGE_NAME     = 'hanush8938/flask-jenkins-demo'
        CONTAINER_NAME = 'php-demo'
        APP_PORT       = '9000'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hanush8938-creator/finalllll.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                        -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                        -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        set +x

                        echo "$DOCKER_PASS" | docker login \
                            -u "$DOCKER_USER" --password-stdin

                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${IMAGE_NAME}:latest

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        -p ${APP_PORT}:80 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}

                    docker ps
                '''
            }
        }
    }

    post {
        success {
            echo 'PHP application deployed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }

        always {
            echo 'Jenkins pipeline execution completed.'
        }
    }
}
