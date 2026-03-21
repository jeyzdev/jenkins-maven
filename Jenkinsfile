pipeline {
    agent any // ใช้ agent any ตัวเดียวที่ด้านบนสุด

    environment {
        REGISTRY_USER = "jeyzdev"
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
        DOCKER_CREDS = credentials('docker-hub-creds')
    }

    stages {
        stage('Deploy to Prod') {
            steps {
                script {
                    // Login
                    sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
                    
                    // สั่งรัน Container โดยใช้ไฟล์ .env ที่เราสร้างไว้บนเครื่อง
                    sh """
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true
                    docker run -d \
                      --name ${IMAGE_NAME} \
                      -p 8081:8080 \
                      --env-file /home/ubuntu/app/.env \
                      ${REGISTRY_USER}/${IMAGE_NAME}:latest
                    """
                    
                    // Logout ทันทีหลังจากสั่งงานเสร็จใน stage นี้เลย
                    sh "docker logout"
                }
            }
        }
    }

    post {
        always {
            // เหลือแค่ล้าง Workspace ก็พอครับ
            cleanWs()
        }
    }
}
