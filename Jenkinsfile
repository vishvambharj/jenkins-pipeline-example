pipeline {
    agent any
    tools {
        maven 'MAVEN'  // Make sure this corresponds to a valid Maven installation in Jenkins
    }

    stages {
        stage('Build') {
            steps {
                echo 'Hello World'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-jenkins', url: 'https://github.com/vishvambharj/jenkins-pipeline-example.git']])
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage('Test') {
            steps {
                echo 'Running Tests'
                // Run tests using Maven
                sh "mvn test"
            }
        }

        stage('Deliver') {
            steps {
                echo 'Delivering the build artifacts'

                // Example: Archive build artifacts (e.g., JAR, WAR files) from the target/ directory
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true

                // Example: Deploy to a remote server (e.g., SCP, FTP, or deploy to a container)
                // Here's a basic example of copying the artifact to a remote server using SCP:
                script {
                    // Assuming you have SSH credentials setup in Jenkins (e.g., "my-ssh-credentials")
                    sh 'scp -i /path/to/your/ssh/key target/your-app.jar user@remote-server:/path/to/deploy'
                }
            }
        }
    }

    post {
        always {
            // Capture the test results and display them in Jenkins
            junit(
                allowEmptyResults: true,
                testResults: 'test-reports/*.xml'  // Ensure this path matches where your test results are stored
            )
        }
    }
}
