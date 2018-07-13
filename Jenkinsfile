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
       	stage('build & SonarQube Scan') {
    		 steps {
    			 withSonarQubeEnv('SonarQubeTest') {
      			 sh 'mvn clean package sonar:sonar'}
    			 // SonarQube taskId is automatically attached to the pipeline context
    			 
    			 // script {
     				// withSonarQubeEnv('SonarQube') {
         				// bat "sonar-scanner-3.2.0.1227-windows/bin/sonar-scanner.bat" 
    				// }
			 	//  }  
			 }	
  		}
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
