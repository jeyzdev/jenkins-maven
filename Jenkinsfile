pipeline {
    agent any 

    environment {
        REGISTRY_USER = "jeyzdev"
        IMAGE_NAME    = "spring-boot-app"
        DOCKER_HUB    = credentials('docker-hub-creds')
        // URL ของ Repo ที่เก็บ Source Code ของแอปฯ
        APP_REPO_URL  = "https://github.com/jeyz9/store-mate-api.git" 
    }

    stages {
        stage('Checkout App Source') {
            steps {
                // ล้างโฟลเดอร์ 'app-source' ก่อนดึงใหม่
                dir('app-source') {
                    deleteDir()
                    // ดึง Code จาก Repo แอปฯ มาลงที่โฟลเดอร์ app-source
                    checkout([$class: 'GitSCM', 
                        branches: [[name: '*/main']], 
                        userRemoteConfigs: [[url: "${APP_REPO_URL}"]]
                    ])
                }
            }
        }

        stage('Build & Push') {
            steps {
                script {
                    // เข้าไปรัน Docker Build ข้างในโฟลเดอร์ที่เพิ่งดึงมา
                    dir('app-source') {
                        sh "echo ${DOCKER_HUB_PSW} | docker login -u ${DOCKER_HUB_USR} --password-stdin"
                        
                        // ตอนนี้ Docker จะเห็น Dockerfile เพราะเราอยู่ในโฟลเดอร์ app-source แล้ว
                        sh "docker build -t ${REGISTRY_USER}/${IMAGE_NAME}:latest ."
                        sh "docker push ${REGISTRY_USER}/${IMAGE_NAME}:latest"
                    }
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
