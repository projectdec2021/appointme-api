pipeline {
  agent any 
    stages {
      stage('SonarQube analysis') {
        environment{
               scannerHome = tool 'sonar-scanner'
          }
        steps { 
          withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"     
           }
        }
     } //stage
      stage("Quality Gate") {
            steps {
              timeout(time: 10, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
          } //end of stage
      
      stage("Docker build") {
            steps {
              sh "sudo docker -t appoint-api:${BUILD_NUMBER} ."
            }
          } //end of stage
     
   }
}
