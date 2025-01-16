pipeline {
    agent any
    tools {
        maven 'MAVEN'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Hello World'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'jenkins-jenkins ', url: 'https://github.com/vishvambharj/jenkins-pipeline-example.git']])
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
    }
    post {
        always {
            junit(
                allowEmptyResults:true,
                testResults: 'test-reports/.xml'
                )
            }
        }
    }
