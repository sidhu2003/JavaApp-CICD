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
	stage('Sonar Analysis') {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
               withSonarQubeEnv('sonar') {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=login-app \
                   -Dsonar.projectName=login-app \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/'''
              }
            }
        }
    }
}
