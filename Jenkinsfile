pipeline {
    agent any 

    environment {
        REGISTRY_USER = "jeyzdev"
        IMAGE_NAME    = "spring-boot-app"
        DOCKER_HUB    = credentials('docker-hub-creds')
    }

    stages {
        stage('Build & Push') {
            steps {
                script {
                    sh "echo ${DOCKER_HUB_PSW} | docker login -u ${DOCKER_HUB_USR} --password-stdin"
                    sh "docker build -t ${REGISTRY_USER}/${IMAGE_NAME}:latest ."
                    sh "docker push ${REGISTRY_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                sh """
                docker stop ${IMAGE_NAME} || true
                docker rm ${IMAGE_NAME} || true
                docker run -d \
                  --name ${IMAGE_NAME} \
                  -p 8081:8080 \
                  --restart always \
                  --env-file /home/ubuntu/app/.env \
                  ${REGISTRY_USER}/${IMAGE_NAME}:latest
                """
                sh "docker logout"
            }
        }
    }
    post { always { cleanWs() } }
}
