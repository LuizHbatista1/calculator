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

				stage("Docker push") {
    				steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            			sh '''
                      sudo usermod -aG docker $USER
                			echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                			docker tag fa34silva824/calculator fa34silva824/calculator:latest
                			docker push fa34silva824/calculator:latest
            				'''
        					}				
    					}
					}

     }

}
