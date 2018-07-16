pipeline {
    agent any
    tools {
        maven 'Maven 3.5.4'
        jdk 'JDK 8u172'
        
    }
    
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
       	}
       	// stage('build & SonarQube Scan') {
    		 // steps {
    			 // withSonarQubeEnv('SonarQubeTest') {
      			 // sh 'mvn clean package sonar:sonar'}
    			 // SonarQube taskId is automatically attached to the pipeline context
    			 
    			 // script {
     				// withSonarQubeEnv('SonarQube') {
         				// bat "sonar-scanner-3.2.0.1227-windows/bin/sonar-scanner.bat" 
    				// }
			 	//  }  
			 // }	
  		// }
  		// stage("Quality Gate") {
            // steps {
                // timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                    // waitForQualityGate abortPipeline: true
                // }
            // }
        // }
        // Note: Jenkins still cannot communicate with the SonarQube server. The "Quality Gate" stage should abort the pipeline if the Quality Gate does not pass.
        // Quality Gate stage will not work unless the webhook inside SonarQube is working properly.
        stage ('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=false install'
                //change the above statement to false to stop the build if a test fails
                // build job: '../PipelineTestMultiBranch/branch-off-CE-fix-again', wait: false
               
            }
            post {
                always {
                    junit 'target/surefire-reports/**/*.xml'
                }
                success {
                	archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
		stage ('User OK') {
        	steps {
	        	script {
	            	if (env.BRANCH_NAME == 'CE-fix-again') {
	            		input message: 'Is this build ok?', ok: 'Yes', submitter: 'mtross,carolynelliott'
	                }
	                else {
	                	input message: 'Would you like to analyze with SonarQube?', ok: 'Yes', submitter: 'mtross,carolynelliott'
	                }
	        	}
        	}
        }
        
 stage('Release and publish artifact') {
            // when {
                // check if branch is master
               //  branch 'master'
            // }
            steps {
                // create the release version then create a tage with it , then push to nexus releases the released jar
                script {
                    def mvnHome = tool 'Maven 3.5.4' //
                    if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
                        def v = getReleaseVersion()
                        releasedVersion = v;
                        if (v) {
                            echo "Building version ${v} - so released version is ${releasedVersion}"
                        }
                        // jenkins user credentials ID which is transparent to the user and password change
                        sshagent(['0000000-3b5a-454e-a8e6-c6b6114d36000']) {
                            sh "git tag -f v${v}"
                            sh "git push -f --tags"
                        }
                        sh "'${mvnHome}/bin/mvn' -Dmaven.test.skip=true  versions:set  -DgenerateBackupPoms=false -DnewVersion=${v}"
                        sh "'${mvnHome}/bin/mvn' -Dmaven.test.skip=true clean deploy"

                    } else {
                        error "Release is not possible. as build is not successful"
                    }
                }
            }
        }
stage('Deploy to Acceptance') {
    // when {
                // check if branch is master
       // branch 'master'
    // }
        steps {
            script {
                 if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
                     timeout(time: 3, unit: 'MINUTES') {
                            //input message:'Approve deployment?', submitter: 'it-ops'
                     input message: 'Approve deployment to UAT?'
                     }
                     timeout(time: 3, unit: 'MINUTES') {
                            //  deployment job which will take the relasesed version
                     if (releasedVersion != null && !releasedVersion.isEmpty()) {
                                // make the applciation name for the jar configurable
                        def jarName = "application-${releasedVersion}.jar"
                        echo "the application is deploying ${jarName}"
                                // NOTE : DO NOT FORGET to create your UAT deployment jar , check Job AlertManagerToUAT in Jenkins for reference
                                // the deployemnt should be based into Nexus repo
                        build job: 'AApplicationToACC', parameters: [[$class: 'StringParameterValue', name: 'jarName', value: jarName], [$class: 'StringParameterValue', name: 'appVersion', value: releasedVersion]]
                        echo 'the application is deployed !'
                     }
                     else {
                        error 'the application is not  deployed as released version is null!'
                     }

                     }
                    }
                }
            }
        }

        stage ('Deploy') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo "does the push go downstream?"
            }
        }
}    
    }
