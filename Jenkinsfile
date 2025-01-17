pipeline {
    agent any
    tools {
        maven 'MAVEN'  // Ensure Maven tool is properly configured
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
                sh "mvn test"
            }
        }

        stage('Deliver') {
            steps {
                echo 'Delivering the build artifacts'
                archiveArtifacts allowEmptyArchive: true, artifacts: 'target/*.jar', followSymlinks: false

                script {
                    def jarFile = sh(script: "ls target/*.jar", returnStdout: true).trim()
                    if (jarFile) {
                        // Update the SSH key path and server hostname as needed
                        sh "scp -i /path/to/your/actual/ssh/key ${jarFile} user@remote-server:/path/to/deploy"
                    } else {
                        error "No JAR file found to deploy!"
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                def testResults = findFiles(glob: 'test-reports/*.xml')
                if (testResults) {
                    junit testResults: 'test-reports/*.xml', allowEmptyResults: true
                } else {
                    echo 'No test results found.'
                }
            }
        }
    }
}
