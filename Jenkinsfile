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
      }
    }
    stage('Build') {
    	steps {
        sh 'mvn -Dmaven.test.failure.ignore=false install'
        sh 'mvn -Dmaven.test.failure.ignore clean package'
      	}
      post {
        always {
          junit 'target/surefire-reports/**/*.xml'

        }
        success {
          archiveArtifacts(artifacts: 'target/*.jar', fingerprint: true)

        }
        }

		 stage('Publish') {
			  steps {
				  //script {
					// nexusArtifactUploader artifacts: [[artifactId: 'gs-spring-boot', classifier: '', file: 'target/gs-spring-boot-0.1.0.jar', type: 'jar']], credentialsId: 'admin', groupId: 'org.springframework', nexusUrl: '10.0.1.153:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'releases', version: '0.1.0'
				  //}
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
                echo "trigger build?"
            }
      }
      
    stage('Publish') {
      steps {
        script {
          nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/gs-spring-boot-0.1.0.jar']], mavenCoordinate: [artifactId: 'gs-spring-boot', groupId: 'org.springframework', packaging: 'jar', version: '0.1.0']]]
        }

      }
    }
    stage('Retrieve Artifact from Nexus') {
      steps {
        script {
          artifactResolver artifacts: [artifact(artifactId: 'gs-spring-boot', groupId: 'org.springframework', version: '0.1.0')], targetDirectory: 'src'
        }

      }
    }
    stage('Deploy') {
      when {
        expression {
          env.BRANCH_NAME == 'master'
        }
      steps {
        echo 'does this edit go to bitbucket?'
      }
    }
  }
  
}