pipeline {
    agent any
    
    environment {
        // ตั้งชื่อ user ให้ตรงกับ Registry ของคุณ (เช่น jeyzdev)
        REGISTRY_USER = "jeyzdev" 
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
        // แนะนำให้สร้าง Credentials ใน Jenkins ชื่อ 'docker-hub-creds' เก็บ Username/Password ไว้
        DOCKER_CREDS = credentials('docker-hub-creds')
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code & Dockerfile') {
            steps {
                // Checkout Source Code
                dir('app-code') {
                    git branch: 'main', url: 'https://github.com/jeyz9/store-mate-api.git'
                }
                // Checkout Repo ที่เก็บ Dockerfile (ถ้าแยกกัน)
                dir('devops-repo') {
                    git branch: 'main', url: "https://github.com/${REGISTRY_USER}/docker-config.git"
                }
            }
        }

        stage('Docker Login') {
            steps {
                // ใช้ตัวแปรจาก Credentials เพื่อ Login เข้า Docker Hub
                sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
            }
        }

        stage('Build & Push to Prod') {
            steps {
                script {
                    // Build โดยใช้ไฟล์จาก devops-repo และ context จาก app-code
                    // Tag image เป็น jeyzdev/spring-boot-app:version
                    sh """
                    docker build -t ${REGISTRY_USER}/${IMAGE_NAME}:${VERSION} \
                                 -t ${REGISTRY_USER}/${IMAGE_NAME}:latest \
                                 -f devops-repo/Dockerfile app-code/
                    """
                    
                    // Push ทั้งเวอร์ชั่นเลข Build และ Tag 'latest'
                    sh "docker push ${REGISTRY_USER}/${IMAGE_NAME}:${VERSION}"
                    sh "docker push ${REGISTRY_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        always {
            // Logout หลังจบงานเพื่อความปลอดภัย
            sh "docker logout"
            cleanWs()
        }
        success {
            echo "Successfully pushed ${REGISTRY_USER}/${IMAGE_NAME}:${VERSION} to Registry"
        }
    }
}
