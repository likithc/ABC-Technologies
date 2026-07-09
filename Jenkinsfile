pipeline {
    agent any

    tools {
        // Ensures Maven and SonarQube Scanner are available
        maven 'Maven 3.x'
        sonarQubeEnv 'SonarQube' // Must match the name in Jenkins Global Tool Configuration
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean & Compile') {
            steps {
                // Cleans previous builds and compiles the Java source code
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                // Runs the existing unit tests provided by the developers
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Triggers SonarQube scanner using Maven plugin
                // It automatically pushes metrics to your SonarQube server
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Package') {
            steps {
                // Packages the application into the target/student.jar file
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                // Builds the Docker image using your Dockerfile
                sh 'docker build -t abc-tech-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application container...'
                // Stops and removes any previous container running on port 8080 to prevent conflicts
                sh 'docker rm -f abc-app-container || true'
                // Runs the newly built image
                sh 'docker run -d -p 8080:8080 --name abc-app-container abc-tech-app:${BUILD_NUMBER}'
            }
        }
    }
}
