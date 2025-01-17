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

