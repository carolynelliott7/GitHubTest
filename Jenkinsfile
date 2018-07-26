pipeline {
  agent any
  
  tools {
    //maven 'Maven 3.0.4'
    jdk 'JDK 8u172'
  }
  triggers {
    githubPush()
  }
  
  stages {
    stage('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
  	   	//script {
  	   		//configFileProvider([configFile('46f15e2d-ebc5-4407-b7ef-058b54981571')]) {
    		//sh 'mvn -s $MAVEN_SETTINGS clean package'
			//}
  	   	//}
      }
    }
    stage('Build') {
    	steps ('Build'){
        sh 'mvn -Dmaven.test.failure.ignore=false install'
        //commenting out next line bc repeating config file command? 7/23
        // sh 'mvn -Dmaven.test.failure.ignore clean package'
      	}
      post ('Unit Tests'){
        always ('Unit Testing'){
          junit 'target/surefire-reports/**/*.xml'
        }
        success {
          archiveArtifacts(artifacts: 'target/*.jar', fingerprint: true)
          archiveArtifacts(artifacts: 'settings.xml', fingerprint: true)
        }
      }
     }
  
     
     stage ('Test') {
     	steps {
     		sh 'echo "Record Unit Tests"'
     	}
     }
     
     stage ('User OK') {
        	steps {
	        	script {
	            	if (env.BRANCH_NAME == 'CE-fix-again') {
	            		input message: 'Would you like to continue to release?', ok: 'Yes', submitter: 'mtross,carolynelliott'
	                }
	                else {
	                	input message: 'Would you like to analyze with SonarQube?', ok: 'Yes', submitter: 'mtross,carolynelliott'
	                }
	        	}
        	}
        }
     

		 stage('Release') {
			  
			steps ('Deploy to Nexus'){
				//script {
					//withCredentials([file(credentialsId: '4cdf43f8-1ce5-4b3c-ab88-e5052b970d45', variable: 'USERNAME'), file(credentialsId: '4cdf43f8-1ce5-4b3c-ab88-e5052b970d45', variable: 'PASSWORD')]) {
			  			//sh 'mvn deploy -Dusername=$USERNAME -password=$PASSWORD'
			  		//}
			  		
			  	//sh 'mvn deploy:deploy-file -DgroupId=org.springframework -DartifactId=gs-spring-boot -Dversion=0.1.0 -DgeneratePom=true -Dpackaging=jar -DrepositoryId=NexusServer -Durl=http://10.0.1.153:8081/nexus/content/repositories/releases/ -Dfile=pom.xml'
			  	//sh 'mvn deploy -DrepositoryId=NexusServer -Durl=http://10.0.1.153:8081/nexus/content/repositories/releases/org/springframework/gs-spring-boot/'
			  	sh 'echo "Release"'
			}
		}	
			  		
				  //script {
					  // withCredentials([file(credentialsId: '4cdf43f8-1ce5-4b3c-ab88-e5052b970d45', variable: 'username'), file(credentialsId: '4cdf43f8-1ce5-4b3c-ab88-e5052b970d45', variable: 'password')]) {
							//nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot', classifier: '', file: 'target/gs-spring-boot-0.1.0.jar', type: 'jar']], credentialsId: '62641c58-98c7-426b-9f26-bf9a09b653cc', groupId: 'org.springframework', nexusUrl: '10.0.1.153:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'releases', version: '0.1.0'
					  //credentials id: 4cdf43f8-1ce5-4b3c-ab88-e5052b970d45
					  //}
				  //}
					 //script {
						// nexusPublisher nexusInstanceId: 'NexusTestServer', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/gs-spring-boot-0.1.0.jar']], mavenCoordinate: [artifactId: 'gs-spring-boot', groupId: 'org.springframework', packaging: 'jar', version: '0.1.0']]]
					 	//}
			 //}
		//}
		 
		 
		//stage ('Retrieve Artifact from Nexus'){
			// steps {
				// sh 'mvn install:install-file'
				//sh 'mvn org.apache.maven.plugins:maven-dependency-plugin:2.8:get  -DrepoUrl=http://10.0.1.153:8081/nexus/ -Dartifact=org.springframework:gs-spring-boot:0.1.0:jar -Dtransitive=false'
				// script {
					// artifactResolver artifacts: [artifact(artifactId: 'gs-spring-boot', groupId: 'org.springframework', version: '0.1.0')], targetDirectory: 'src'
    			// }
    		 //}
    	//}
    	
        //stage ('Deploy') {
           // when {
             //   expression { env.BRANCH_NAME == 'master' }
            //}
            //steps {
              //  echo "please send to GitHub?"
            //}
      	//}
  }
  
}