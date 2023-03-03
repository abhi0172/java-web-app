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
				sh 'sudo docker tag java-repo:$BUILD_TAG abhishek0322/pipeline-java:$BUILD_TAG'
				}
			}
				
	
		stage("Docker Hub") {
			steps {
			withCredentials([string(credentialsId: 'docker', variable: 'docker_var')]) {
				sh 'sudo docker login -u abhishek0322 -p ${docker_var}'
				sh 'sudo docker push abhishek0322/pipeline-java:$BUILD_TAG'
				}
			}	

		}
		stage("QAT Testing") {
			steps {
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
				sh 'sudo docker run -dit -p 9001:8080  abhishek0322/pipeline-java:$BUILD_TAG'
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
			 sshagent(['ec2user']) {
			    sh 'ssh -o StrictHostKeyChecking=no ec2-user@3.90.81.92 sudo docker rm -f $(sudo docker ps -a -q)' 
	                    sh "ssh -o StrictHostKeyChecking=no ec2-user@3.90.81.92 sudo docker run  -d  -p  49153:8080  abhishek0322/pipeline-java:$BUILD_TAG"
				}
			}
		}
	}
}

