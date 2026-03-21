pipeline {
    agent any
    environment {
        REGISTRY = "jeyzdev"
        IMAGE_NAME = "spring-boot-app"
        VERSION = "${env.BUILD_NUMBER}"
    }
    stages {
        stage('Build & Push') {
            steps {
                script {
                    sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:${VERSION} ."
                    sh "docker push ${REGISTRY}/${IMAGE_NAME}:${VERSION}"
                }
            }
        }
        stage('Deploy to Prod') {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'prod-server', transfers: [sshTransfer(remoteDirectory: '', sourceFiles: 'docker-compose.yml')], execCommand: 'docker-compose pull && docker-compose up -d')])
            }
        }
    }
}
