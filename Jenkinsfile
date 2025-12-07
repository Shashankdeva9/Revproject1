pipeline {
    agent any
    
    tools {
        maven 'Maven3'
        jdk 'JDK17'
    }
    
    environment {
        DOCKER_IMAGE = 'revtickets-backend'
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Shashankdeva9/Revproject1.git'
            }
        }
        
        stage('Build JAR') {
            steps {
                dir('backend') {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Build Docker Image') {
            when {
                expression { return fileExists('C:\\Program Files\\Docker\\Docker\\Docker Desktop.exe') }
            }
            steps {
                dir('backend') {
                    bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    bat "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
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
                    bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    bat "docker push ${DOCKER_IMAGE}:latest"
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
