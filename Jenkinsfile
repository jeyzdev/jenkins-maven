pipeline {
    agent any // เปลี่ยนจาก none เป็น any เพื่อให้ post work ได้

    environment {
        REGISTRY_USER = "jeyzdev" 
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
        // สมมติว่าสร้าง Credentials ใน Jenkins ชื่อ 'docker-hub-creds' แล้ว
        DOCKER_CREDS = credentials('docker-hub-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                dir('app-code') {
                    git branch: 'main', url: 'https://github.com/jeyz9/store-mate-api.git'
                }
                // ถ้า Dockerfile อยู่ใน repo เดียวกันกับ code ให้เอา dir นี้ออก
                // แต่ถ้าแยกกัน ให้ checkout ลงมาคนละ folder
            }
        }

        stage('Docker Login & Build') {
            steps {
                script {
                    // Login โดยใช้ credentials
                    sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
                    
                    // Build image
                    sh "docker build -t ${REGISTRY_USER}/${IMAGE_NAME}:${VERSION} ./app-code"
                }
            }
        }

        stage('Push to Registry') {
            steps {
                sh "docker push ${REGISTRY_USER}/${IMAGE_NAME}:${VERSION}"
            }
        }
    }

    post {
        always {
            // ตอนนี้จะไม่ออก error 'MissingContextVariableException' แล้ว
            sh "docker logout"
            cleanWs()
        }
    }
}
