pipeline {
    // แนะนำให้ตั้ง agent any ไว้ที่นี่เลยเพื่อให้ครอบคลุมทุก stage และ post
    agent any 

    environment {
        REGISTRY_USER = "jeyzdev"
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
        DOCKER_CREDS = credentials('docker-hub-creds')
    }

    stages {
        stage('Deploy') {
            steps {
                script {
                    // Login ก่อน
                    sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
                    
                    // รัน deploy โดยดึงไฟล์ .env จากเครื่อง host
                    sh """
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true
                    docker run -d \
                      --name ${IMAGE_NAME} \
                      -p 8081:8080 \
                      --env-file /home/ubuntu/app/.env \
                      ${REGISTRY_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            // คราวนี้จะไม่ออก error แล้ว เพราะมี agent any คุมอยู่ด้านบน
            node {
                sh "docker logout"
                cleanWs()
            }
        }
    }
}
