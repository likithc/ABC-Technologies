pipeline {
    agent any

    tools {
        // Ensures Maven 3.x is available from your Jenkins Global Tool Configuration
        maven 'Maven3' 
    }

    stages {
        stage('Checkout') {
            steps {
                // Pulls the latest project repository code from GitHub
                checkout scm
            }
        }

        stage('Clean & Compile') {
            steps {
                // Prepares the target workspace and compiles your Java sources
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                // Executes the developer-written unit tests
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Authenticates using the -Dsonar.login property for version compatibility
                // REPLACE 'YOUR_SONARQUBE_TOKEN' with the key you copied from your dashboard
                sh 'mvn sonar:sonar -Dsonar.host.url=http://3.238.143.114:9000 -Dsonar.login=squ_43b1121948dee80ce5f3bd934b0db02f6e19c00d'
            }
        }

        stage('Package') {
            steps {
                // Compiles and builds the production jar file while skipping test re-execution
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                // Packages your application into a Docker image using your Dockerfile
                sh 'docker build -t abc-tech-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application container...'
                // Removes old iterations of the application container to free up the host port
                sh 'docker rm -f abc-app-container || true'
                // Spins up the new image build on port 8080
                sh 'docker run -d -p 8080:8080 --name abc-app-container abc-tech-app:${BUILD_NUMBER}'
            }
        }
    }

    post {
        always {
            // Cleans up the workspace on the Jenkins agent to save disk space
            cleanWs()
        }
    }
}
