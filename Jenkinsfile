pipeline {
    agent any

    environment {
        IMAGE_NAME = "spring-boot-app"
        CONTAINER_NAME = "spring-boot-container"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jeyz9/store-mate-api.git'
            }
        }

        stage('Build Artifact') {
            steps {
                // ใช้ Double Quote เพื่อความชัวร์ในการดึงค่า Env (ถ้ามี)
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Docker Build & Cleanup') {
            steps {
                // สร้าง Image ใหม่ และลบ Image เก่าที่ไม่มีชื่อ (dangling) เพื่อประหยัดพื้นที่
                sh """
                docker build -t ${IMAGE_NAME} .
                docker image prune -f
                """
            }
        }

        stage('Deploy') {
            steps {
                // รวม Stop และ Run ไว้ด้วยกันเพื่อความต่อเนื่อง
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 8080:8080 \
                --name ${CONTAINER_NAME} \
                ${IMAGE_NAME}
                """
            }
        }
    }

    post {
        success {
            echo '🚀 Deploy Success!'
        }
        failure {
            echo '❌ Build Failed!'
        }
        always {
            // ลบไฟล์ใน Workspace เพื่อไม่ให้ Disk เต็ม (เลือกใช้ตามความเหมาะสม)
            cleanWs()
        }
    }
}
