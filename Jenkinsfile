pipeline {
    agent any

    tools {
        // Ensures Maven is available. Name must match Jenkins Global Tool Configuration.
        maven 'Maven3' 
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls the latest code from your GitHub repository
                checkout scm
            }
        }

        stage('Clean & Compile') {
            steps {
                // Cleans old build artifacts and compiles Java classes
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                // Executes existing unit tests provided by developers
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Authenticates and sends quality metrics to your running SonarQube container
                // REPLACE 'YOUR_SONAR_TOKEN_HERE' with the token you generated in SonarQube
                sh 'mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_SONAR_TOKEN_HERE'
            }
        }

        stage('Package') {
            steps {
                // Packages the compiled code into 'target/student.jar', skipping test re-execution
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                // Builds the container image using the Dockerfile in the root workspace
                sh 'docker build -t abc-tech-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application container...'
                // Safely stops and removes any previously running container to avoid port conflicts
                sh 'docker rm -f abc-app-container || true'
                // Spins up the newly built container on port 8080
                sh 'docker run -d -p 8080:8080 --name abc-app-container abc-tech-app:${BUILD_NUMBER}'
            }
        }
    }
    
    post {
        always {
            // Cleans up the Jenkins workspace directory to preserve disk space
            cleanWs()
        }
    }
}
