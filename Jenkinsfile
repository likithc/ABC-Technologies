pipeline {
    agent any

    tools {
        // Fixed: Matches your server's configured tool name
        maven 'Maven3' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean & Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // The environment configuration is properly wrapped directly inside the step here
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t abc-tech-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker rm -f abc-app-container || true'
                sh 'docker run -d -p 8080:8080 --name abc-app-container abc-tech-app:${BUILD_NUMBER}'
            }
        }
    }
}
