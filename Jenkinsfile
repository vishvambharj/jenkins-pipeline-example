pipeline {
    agent any
    tools {
        maven 'maven'  // Make sure this corresponds to a valid Maven installation in Jenkins
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
                // Archive build artifacts
                archiveArtifacts allowEmptyArchive: true, artifacts: 'target/*.jar', followSymlinks: false
                
                // Ensure the JAR file exists before attempting SCP
                script {
                    def jarFile = sh(script: "ls target/*.jar", returnStdout: true).trim()
                    if (jarFile) {
                        // SCP to remote server
                        sh "scp -i /path/to/your/ssh/key ${jarFile} user@remote-server:/path/to/deploy"
                    } else {
                        error "No JAR file found to deploy!"
                    }
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

