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
      
      stage("Docker build & push") {
            steps {
              withCredentials([usernamePassword(credentialsId: 'priya-docker-secret', passwordVariable: 'pass', usernameVariable: 'user')]) {
                sh """ 
                  
                  sudo docker build -t priya4/appoint-api:${BUILD_NUMBER} .
                  sudo docker login -u ${user} -p ${pass}
                  docker push priya4/appoint-api:${BUILD_NUMBER}
                """
              }              
            }
          } //end of stage
      
     
   }
}
