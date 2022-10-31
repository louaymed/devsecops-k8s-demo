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
          sh "mvn  sonar:sonar -Dsonar.projectKey=devsecops  -Dsonar.host.url=http://20.38.35.181:9000  -Dsonar.login=sqp_ea8d735b3cf2e9cb0c046e8bbb9f829a8c680860"
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
