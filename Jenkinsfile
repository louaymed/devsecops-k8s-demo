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
              sh "mvn org.pitest:pitest-maven:mutaionCovearge"
            }
                post {
				always {
          pitmutaion.mutationStatsfile:.'**target/pit-reports/**/mutations.xml'
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
