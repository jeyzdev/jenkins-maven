pipeline {
    agent none // ปิด global agent เพื่อไปคุมเองในแต่ละส่วน

    environment {
        REGISTRY_USER = "jeyzdev"
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
        DOCKER_CREDS = credentials('docker-hub-creds')
    }

    stages {
        stage('Deploy') {
            agent any // จองเครื่องเฉพาะตอนทำงาน
            steps {
                script {
                    sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
                    
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
            // ใช้ script block เพื่อช่วยให้รัน sh ใน post ได้นิ่งขึ้น
            script {
                node('built-in' || 'master') { // ระบุชื่อ node พื้นฐานของ Jenkins
                    sh "docker logout || true"
                    cleanWs()
                }
            }
        }
    }
}
