pipeline {
	agent any
	tools {
	jdk 'java17'
	maven 'Maven3' 
	}
	stages {
		stage("Clean-Up WorkSpace"){
  			steps {
  			cleanWs()
			}
		}
		stage ("Checkout SCM GitHub"){
			steps{
				git branch: 'main', credentialsId: 'GitHub-Credentials', url: 'https://github.com/tchounke/register-app'
			}
		}
		stage("Build the App"){
			steps{
				sh "mvn clean package"
			}
		}
		stage("Test the App"){
			steps{
				sh "mvn test"
			}
		}
		stage("SonarQube Scanning"){
			steps{
				script{
					withSonarQubeEnv(credentialsId: 'Jenkins-Sonar') {
 					sh "mvn sonar:sonar"
					}
				}
			}
		}
		stage("Quality Qate Check"){
			steps{
				script{
					waitForQualityGate abortPipeline: false, credentialsId: 'Jenkins-Sonar'
				}
			}
		}
	}
}
