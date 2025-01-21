pipeline {
agent any
tools {
maven 'MAVEN'
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
                // Add the remote server's SSH key to known_hosts to avoid verification issues
                sh "ssh-keyscan -H 51.20.135.206 >> /var/lib/jenkins/.ssh/known_hosts"

                def jarFile = sh(script: "ls target/*.jar", returnStdout: true).trim()
                if (jarFile) {
                    echo "Found JAR file: ${jarFile}"
                    // Proceed with SCP to deploy the file
                    sh "scp -i /var/lib/jenkins/.ssh/id_rsa ${jarFile} ubuntu@51.20.135.206:/var/www/myapp/"
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

