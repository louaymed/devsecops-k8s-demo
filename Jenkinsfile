pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }  
              stage('Unit test - Junit and jacoco') {
            steps {
              sh "mvn test"
            }
                post {
				always {
          junit 'target/surefire-reports/*.xml'
	  jacoco execPattern: 'target/jacoco.exec'
				       }    
			    } 
        } 
	  stage('Mutation Tests - PIT') {
            steps {
              sh "mvn org.pitest:pitest-maven:mutationCoverage"
            }
                post {
				always {
          pitmutation mutationStatsFile: '**target/pit-reports/**/mutations.xml'
				       }    
			    } 
        } 
	 stage('SonarQube - SAST') {
       steps {
        withSonarQubeEnv('SonarQube') {
          sh "mvn clean verify sonar:sonar \
  		-Dsonar.projectKey=devsecops \
  		-Dsonar.host.url=http://20.38.35.181:9000 \
 	        -Dsonar.login=sqp_5e52e80583dc047fbaf2e9a4709964755d417ad6"
       }
        timeout(time: 2, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
          }
        }
      }
    }
	  stage('Docker Build and Push') {
                       steps {
                               withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
         			  sh 'printenv'
        			  sh 'docker build -t louay123/devsecops:""$GIT_COMMIT"" .'
         			  sh 'docker push louay123/devsecops:""$GIT_COMMIT""'
         			}
     			  }
	  }
    }
}
