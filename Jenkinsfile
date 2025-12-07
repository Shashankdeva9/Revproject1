pipeline {
    agent any
    
    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }
    
    environment {
        DOCKER_IMAGE_BACKEND = 'shashank092/revtickets-backend'
        DOCKER_IMAGE_FRONTEND = 'shashank092/revtickets-frontend'
        DOCKER_IMAGE_DB = 'shashank092/revtickets-mysql'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Shashankdeva9/Revproject1.git'
            }
        }
        
        stage('Build Backend JAR') {
            steps {
                dir('backend') {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }
        
        stage('Build Backend Docker Image') {
            when {
                expression { return fileExists('C:\\Program Files\\Docker\\Docker\\Docker Desktop.exe') }
            }
            steps {
                dir('backend') {
                    bat "docker build -t ${DOCKER_IMAGE_BACKEND}:${DOCKER_TAG} ."
                    bat "docker tag ${DOCKER_IMAGE_BACKEND}:${DOCKER_TAG} ${DOCKER_IMAGE_BACKEND}:latest"
                }
            }
        }
        
        stage('Build Frontend Docker Image') {
            when {
                expression { return fileExists('C:\\Program Files\\Docker\\Docker\\Docker Desktop.exe') }
            }
            steps {
                dir('frontend') {
                    bat "docker build -t ${DOCKER_IMAGE_FRONTEND}:${DOCKER_TAG} ."
                    bat "docker tag ${DOCKER_IMAGE_FRONTEND}:${DOCKER_TAG} ${DOCKER_IMAGE_FRONTEND}:latest"
                }
            }
        }
        
        stage('Build MySQL Docker Image') {
            when {
                expression { return fileExists('C:\\Program Files\\Docker\\Docker\\Docker Desktop.exe') }
            }
            steps {
                dir('database') {
                    bat "docker build -t ${DOCKER_IMAGE_DB}:${DOCKER_TAG} ."
                    bat "docker tag ${DOCKER_IMAGE_DB}:${DOCKER_TAG} ${DOCKER_IMAGE_DB}:latest"
                }
            }
        }
        
        stage('Push to Registry') {
            when {
                expression { return fileExists('C:\\Program Files\\Docker\\Docker\\Docker Desktop.exe') }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat "docker login -u %USER% -p %PASS%"
                    bat "docker push ${DOCKER_IMAGE_BACKEND}:${DOCKER_TAG}"
                    bat "docker push ${DOCKER_IMAGE_BACKEND}:latest"
                    bat "docker push ${DOCKER_IMAGE_FRONTEND}:${DOCKER_TAG}"
                    bat "docker push ${DOCKER_IMAGE_FRONTEND}:latest"
                    bat "docker push ${DOCKER_IMAGE_DB}:${DOCKER_TAG}"
                    bat "docker push ${DOCKER_IMAGE_DB}:latest"
                }
            }
        }
    }
    
    post {
        success {
            echo 'Build and Docker image creation successful!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
