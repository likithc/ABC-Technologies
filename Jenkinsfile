pipeline {
    agent any

    tools {
        maven 'Maven3' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Skipping SonarQube metrics collection step...'
            }
        }

        stage('Package JAR') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Using absolute path forcing to locate the host Docker runtime execution block
                // If this still fails, we will explicitly add the environment PATH to this command
                sh '/usr/bin/docker build -t springboot-app:latest .'
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying application container on port 8081...'
                sh '/usr/bin/docker rm -f springboot-container || true'
                sh '/usr/bin/docker run -d -p 8081:8080 --name springboot-container springboot-app:latest'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
