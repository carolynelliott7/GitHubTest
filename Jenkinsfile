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
        stage ('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true install'
                //change the above statement to false to stop the build if a test fails
                // build job: '../PipelineTestMultiBranch/branch-off-CE-fix-again', wait: false
               
            }
            post {
                success {
                	
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        stage ('Test Status') {
        	steps {
        		script {
        			AbstractTestResultAction testResultAction = currentBuild.rawBuild.getAction(AbstractTestResultAction.class)
    				if (testResultAction != null) {
    					def failed = testResultAction.failCount
    					if (failed == 0) {
    						archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
    					}
    				}
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
        stage('build & SonarQube Scan') {
    		steps {
    			withSonarQubeEnv('SonarQubeTest') {
      			sh 'mvn clean package sonar:sonar'}
    			// SonarQube taskId is automatically attached to the pipeline context
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
