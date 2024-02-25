def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger'
]

pipeline {
    agent any
	
    tools {
	    maven "MAVEN"
	    jdk "OracleJDK11"
	}

    environment {
        registryCredential = 'ecr:us-east-1:awscreds'
        appRegistry = "817780818534.dkr.ecr.us-east-1.amazonaws.com/login-app"
        loginappRegistry = "https://817780818534.dkr.ecr.us-east-1.amazonaws.com"
        cluster = "login"
        service = "loginappsvc"
    }
	
  stages {
    stage('Fetch code'){
      steps {
        git branch: 'docker', url: 'https://github.com/sidhu2003/login-app.git'
      }
    }


    stage('Test'){
      steps {
        sh 'mvn test'
      }
    }

    stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

    stage('Build App Image') {
       steps {
       
         script {
                dockerImage = docker.build( appRegistry + ":$BUILD_NUMBER", "./")
             }

     }
    
    }

    stage('Upload App Image') {
          steps{
            script {
              docker.withRegistry( loginappRegistry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
     }

     stage('Deploy to ECS'){
        steps {
            withAWS(region: 'us-east-1',credentials: 'awscreds') {
                script {
                   sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
     }

  }
	post {
        always {
            echo 'Slack Notification'
            slackSend channel: '#jenkinscicd',
            color: COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME}"
        }
    }
}
