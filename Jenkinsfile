pipeline {
    agent any
    tools {
	    maven "MAVEN"
	    jdk "OracleJDK11"
	}
    stages{
        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Test'){
            steps {
                sh 'mvn test'
            }

        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Upload Artifact'){
            steps {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: '172.31.89.187:8081',
                        groupId: 'com.example',
                        version: "${env.BUILD_NUMBER}-${env.BUILD_TIMESTAMP}",
                        repository: 'login-app',
                        credentialsId: 'nexuslogin',
                            artifacts: [
                                    [artifactId: 'login-app',
                                    file: 'target/dptweb-1.0.war',
                                     type: 'war']
                            ]
                        )
            }
        }

    }
}
