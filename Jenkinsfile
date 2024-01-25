pipeline {
	
   agent any

   tools {

      maven 'MAVEN3'
      jdk 'OracleJDK11'

   }

   stages {

      stage('Fetch code') {

         steps {

            git 'https://github.com/suhas-RB/maven_java_web_example.git'

         }

      }

      stage('Build the code') {

         steps {

            sh 'mvn install -DskipTests'

         }

      }

      stage('Test code and checkstyle') {

         steps {

            sh 'mvn test'
            sh 'mvn checkstyle:checkstyle'

         }


      }

      stage('Sonar analysis') {

         environment {

	   	     scannerHome = tool 'Sonar4.7'

	   	  }

         steps {

            withSonarQubeEnv('Sonar') {

	            sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Practice1 \
                   -Dsonar.projectName=Practice1 \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/mycompany/app \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''

	         }


         }



      }


      stage('Quality gate') {

         steps {

               timeout(time: 1, unit: 'HOURS') {

	            //Below Parameter indicates whether to set pipeline to UNSTABLE if quality gate fails
	            //true = set peipeline to UNSTABLE . false = dont
	            waitForQualityGate abortPipeline: true

	         }

         }

      }

       stage('Upload Artifact') {
	      steps {

	          nexusArtifactUploader(
                nexusVersion: 'nexus3',
		        protocol: 'http',
		        nexusUrl: '3.209.81.191:8081',
		        groupId: 'QA',
		        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
		        repository: 'practice-repo',
		        credentialsId: 'NexusLogin',
		        artifacts: [
		            [artifactId: 'api_${env.BUILD_ID}',
		             classifier: '',
		             file: 'target/api.war',
		             type: 'war']
        ]
     )


	      }


	   }




   }


}
