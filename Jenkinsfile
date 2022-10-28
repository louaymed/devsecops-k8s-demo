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
	  stage('Docker Build and Push') {
                       steps {
                               withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
         			  sh 'printenv'
        			  sh 'docker build -t louay123/devsecops .'
	 			  sh 'docker tag louay123/devsecops louay123/devsecops:latest'
         			  sh 'docker push louay123/devsecops:latest'
         			}
     			  }
    }
}
