pipeline {
	agent any
	stages {
		stage("SCM") {
			steps {
				git 'https://github.com/abhi0172/java-web-app.git'
				}
			}

		stage("build") {
			steps {
				sh 'sudo mvn dependency:purge-local-repository'
				sh 'sudo mvn clean package'
				}
			}
		stage("Image") {
			steps {
				sh 'sudo docker build -t java-repo:$BUILD_TAG .'
				sh 'sudo docker tag java-repo:$BUILD_TAG srronak/pipeline-java:$BUILD_TAG'
				}
			}
				
	
		
		stage("QAT Testing") {
			steps {
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
				sh 'sudo docker run -dit -p 9001:8080  srronak/pipeline-java:$BUILD_TAG'
				}
			}
		stage("testing website") {
			steps {
				retry(5) {
				sh 'curl --silent http://44.204.111.87:9001/java-web-app/  '
					}
				}
			}

		stage("Approval status") {
			steps {
				script {
					 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
					}
				}	
		}
		stage("Prod Env") {
			steps {
			 sshagent(['ubuntu']) {
			    sh 'ssh -o StrictHostKeyChecking=no ubuntu@44.204.111.87 sudo docker rm -f $(sudo docker ps -a -q)' 
	                    sh "ssh -o StrictHostKeyChecking=no ubuntu@44.204.111.87 sudo docker run  -d  -p  49153:8080  srronak/javatest-app:$BUILD_TAG"
				}
			}
		}
	}
}

