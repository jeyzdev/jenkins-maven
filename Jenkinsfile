pipeline {
    agent {
        docker {
            image 'jenkins/jenkins:lts'
            // ใส่ -u 0 เพื่อสั่งให้ pipeline รันด้วย root ตลอดทั้งสเตจ
            args '-u 0 -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

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

        stage('Docker Build & Cleanup') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME} .
                docker image prune -f
                """
            }
        }

        stage('Deploy') {
            steps {
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
            cleanWs()
        }
    }
}
