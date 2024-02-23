pipeline {
    agent any
    tools {
	    maven "MAVEN"
	    jdk "OracleJDK21"
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
            steps {
                // Run SonarQube analysis
               withSonarQubeEnv(installationName: 'sonar') { 
                    sh 'mvn clean org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
        }
            }
        }
    }
}
