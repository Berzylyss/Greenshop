pipeline {
    agent any

    environment {
        IMAGE_NAME = "berzylyss/greenshop-web"
        DOCKER_CREDENTIALS_ID = 'dockerhub'
        GIT_REPO_URL = 'https://github.com/Berzylyss/Greenshop.git'
        GIT_BRANCH = 'main'
        GIT_WEB_FOLDER = 'greenshop-web'
    }

    stages {

        stage('Checkout Web Code') {
            steps {
                script {
                    sh """
                    rm -rf web-tmp
                    git clone --depth 1 --branch ${GIT_BRANCH} ${GIT_REPO_URL} web-tmp
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('web-tmp/greenshop-web') {
                    script {
                        echo "Construction de l'image Docker web..."
                        docker.build("${IMAGE_NAME}:latest")
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: "${DOCKER_CREDENTIALS_ID}", url: '') {
                    script {
                        echo "Push de l'image vers Docker Hub..."
                        docker.image("${IMAGE_NAME}:latest").push()
                    }
                }
            }
        }

        stage('Deploy Web Container') {
            steps {
                script {
                    echo "Déploiement du conteneur web..."
                    sh """
                    docker rm -f greenshop-web || true
                    docker pull ${IMAGE_NAME}:latest
                    docker run -d --name greenshop-web --network greenshop-net -p 80:80 ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
}
