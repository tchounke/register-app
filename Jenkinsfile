pipeline {
	agent any
	tools {
	jdk 'java17'
	maven 'Maven3' 
	}
	environment{
		APP_NAME = "Register-App-Pipiline"
		RELEASE = "1.0.0"
		DOCKER_USER = "tchounke"
		DOCKER_PASS = "Docker-Credentials"
		IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
		IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
		stage("Docker Build & Push"){
			steps{
				 script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                			}
                		}
			}
		}
	}
}
