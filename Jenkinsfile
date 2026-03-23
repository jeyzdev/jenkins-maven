pipeline {
    agent any 

    environment {
        // REGISTRY_USER = "jeyzdev"
        // IMAGE_NAME    = "spring-boot-app"
        // DOCKER_HUB    = credentials('docker-hub-creds')
        // URL ของ Repo ที่เก็บ Source Code ของแอปฯ
        APP_REPO_URL  = "https://github.com/nabnoey/storemate.git"
        VERCEL_HOOK_URL = credentials('vercel-token')
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

        stage('Install Dependencies') {
          steps {
            script {
                    // เข้าไปรัน Docker Build ข้างในโฟลเดอร์ที่เพิ่งดึงมา
                    dir('app-source') {
                        sh 'npm install'
                    }
                }
          }
        }

        stage('Build') {
            steps {
                script {
                    // เข้าไปรัน Docker Build ข้างในโฟลเดอร์ที่เพิ่งดึงมา
                    dir('app-source') {
                        sh 'npm run build'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh "curl -X POST ${VERCEL_HOOK_URL}"
            }
        }
    }
    post { always { cleanWs() } }
}
