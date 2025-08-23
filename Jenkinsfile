pipeline {

     agent any

     stages {

          stage("Compile") {

               steps {

                    sh "./gradlew compileJava"

               }

          }

          stage("Unit test") {

               steps {

                    sh "./gradlew test"

               }

          }

					stage("Code coverage") {

     					steps {

          					sh "./gradlew jacocoTestReport"

          					publishHTML (target: [

               				reportDir: 'build/reports/jacoco/test/html',

               				reportFiles: 'index.html',

               				reportName: "JaCoCo Report"

          						])

          					sh "./gradlew jacocoTestCoverageVerification"

     								}

						}


					stage("Static code analysis") {

    				 steps {
									 publishHTML (target: [

    	 								reportDir: 'build/reports/checkstyle/',
     									reportFiles: 'main.html',
     									reportName: "Checkstyle Report"

											])

          				 sh "./gradlew checkstyleMain"

     				}

				}

				stage("Package") {

     				steps {

          				sh "./gradlew build"

     			}

				}


        stage("Docker build") {
            steps {
                sh '''
                    sudo docker build -t fa34silva824/calculator .
                '''
            }
        }

				stage("Docker push") {
    				steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            			sh '''
                      
                			echo "$DOCKER_PASS" | sudo docker login -u "$DOCKER_USER" --password-stdin
                			sudo docker tag fa34silva824/calculator fa34silva824/calculator:latest
                			sudo docker push fa34silva824/calculator:latest
            				'''
        					}				
    					}
					}


        stage("install istio and create profile"){

            steps{

                 sh "istioctl install --set profile=default -y"


            }

        }

        stage("deploy to k8s cluster"){

            steps{
                 
                 sh "kubectl apply -f k8s/replicaSet.yaml"
                 sh "kubectl apply -f k8s/service.yaml"
                 sh "kubectl apply -f k8s/gateway.yaml"
                 sh "kubectl apply -f k8s/virtual-service.yaml"

            }

        }

     }

}
