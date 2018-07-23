pipeline {
  agent any
  
  tools {
    maven 'Maven 3.5.4'
    jdk 'JDK 8u172'
  }
  triggers {
    bitbucketPush()
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
    	steps {
        sh 'mvn -Dmaven.test.failure.ignore=false install'
        //commenting out next line bc repeating config file command? 7/23
        // sh 'mvn -Dmaven.test.failure.ignore clean package'
      	}
      post {
        always {
          junit 'target/surefire-reports/**/*.xml'
        }
        success {
          archiveArtifacts(artifacts: 'target/*.jar', fingerprint: true)
          archiveArtifacts artifacts: 'C:\Users\carolynelliott\Documents\apache-maven-3.5.4-bin\apache-maven-3.5.4\conf\settings.xml', caseSensitive: false, fingerprint: true, onlyIfSuccessful: true
        }
      }
     }

		 stage('Publish') {
			  steps {
				  //script {
					// nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot', classifier: '', file: 'target/gs-spring-boot-0.1.0.jar', type: 'jar']], credentialsId: 'admin', groupId: 'org.springframework', nexusUrl: '10.0.1.153:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'releases', version: '0.1.0'
				  //}
				 script {
					 nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/gs-spring-boot-0.1.0.jar']], mavenCoordinate: [artifactId: 'gs-spring-boot', groupId: 'org.springframework', packaging: 'jar', version: '0.1.0']]]
				 }
			 }
		 }
		 
		stage ('Retrieve Artifact from Nexus'){
			steps {
				script {
					artifactResolver artifacts: [artifact(artifactId: 'gs-spring-boot', groupId: 'org.springframework', version: '0.1.0')], targetDirectory: 'src'
    			}
    		}
    	}
    	
        stage ('Deploy') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo "trigger build?7"
            }
      	}
  }
  
}