pipeline {
           agent {
                   label "jenkins-file"
                   }
                   stages {
                            stage ( "pull the code from github" ) {
                                    steps {
                                            git branch: 'main', url: 'https://github.com/devops-rups/jenkinsfile.git'
                                } 
                           }
                           stage ( "package building" ) {
                                   steps {
                                           sh 'sudo mvn dependency:purge-local-repository'
                                           sh 'sudo mvn clean package'

                                }

                         } 
                         stage ( " docker build a images " ) {
                                 steps {
                                         sh 'sudo docker build -t new-java-app:$BUILD_TAG .'
                                         sh 'sudo docker tag new-java-app:$BUILD_TAG rupalikhatri/new-java-app:$BUILD_TAG'


                               }

                        }
                        stage ( "push to docker hub " ) {
                                steps {
                                        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'docker_hub_pass_var', usernameVariable: 'docker_hub_user_var')]) {

                                           sh 'sudo docker login -u ${docker_hub_user_var} -p ${docker_hub_pass_var}'
                                           sh 'sudo docker push rupalikhatri/new-java-app:$BUILD_TAG'
}
                              }
                       }
                       stage ( " testing the pipeline ") {
                               steps {
                                       sh 'sudo docker run -dit --name java-app -p 8080:8080 new-java-app:$BUILD_TAG'
                              }
                       }
                       stage ( " approval status " ) {
                                steps {
                                        input " Please approve to proccess with deployment"

                             }
                      }
                      stage ( " check website " ) {
                              steps {
                                      sh 'sudo curl http://172.31.47.72:8080/java-web-app/'

                        }
                }
		stage ( " prod even" ) {
		        steps {
			        sshagent(['ubuntu']) {
			    	 	sh "ssh -o StrictHostKeyChecking=no ubuntu@13.53.182.192 sudo docker run  -d  -p  49153:8080  rupalikhatri/new-java-app:$BUILD_TAG"
				}

			}
		}
          }
}          
