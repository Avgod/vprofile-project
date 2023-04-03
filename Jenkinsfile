properties([pipelineTriggers([githubPush()])])
pipeline {
    
	agent any
/*	
	tools {
        maven "maven3"
    }
*/	
	
    stages{
        
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

	stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
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

        stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonarscanner4'
          }

          steps {
            withSonarQubeEnv('sonar-pro') {
               sh '''sonar-scanner \
                    -Dsonar.projectKey=9f3ad33a6218a79456dae4087571b1690ac46a90 \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://43.205.7.80:9000 \
                    -Dsonar.login=9f3ad33a6218a79456dae4087571b1690ac46a90'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
        }
    }
}
