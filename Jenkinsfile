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

    post{
        always{
            slacksend(channel: '#jenkinscicd', color: '#0000FF', message: "Job is'${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        
        success{
            slacksend(channel: '#jenkinscicd', color: '#00FF00', message: "Job Success'${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }

        failure{
            slacksend(channel: '#jenkinscicd', color: '#FF0000', message: "Job Failed'${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
    }
}
