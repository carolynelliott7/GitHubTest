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
            }
            post {
                success {
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        stage('build & SonarQube Scan') {
    		withSonarQubeEnv('My SonarQube Server') {
      			sh 'mvn clean package sonar:sonar'
    		} // SonarQube taskId is automatically attached to the pipeline context
  		}
 

		stage("Quality Gate") {
  			timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    			def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    			if (qg.status != 'OK') {
      			error "Pipeline aborted due to quality gate failure: ${qg.status}"
    		}
  		}
        stage ('Deploy') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                echo "one more time!"
            }
        }
}    
    }
}