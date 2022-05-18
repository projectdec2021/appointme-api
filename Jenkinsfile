pipeline {
  agent any 
  environment {
       nexus_url = '35.232.16.59'
    }
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
              withCredentials([usernamePassword(credentialsId: 'nexus-secret', passwordVariable: 'pass', usernameVariable: 'user')]) {
                sh """ 
                  
                  sudo docker build -t ${nexus_url}:8082/appoint-api:${BUILD_NUMBER} .
                  sudo docker login  -u ${user} -p ${pass} ${nexus_url}:8082
                  sudo docker push ${nexus_url}:8082/appoint-api:${BUILD_NUMBER}
                  sudo docker rmi ${nexus_url}:8082/appoint-api:${BUILD_NUMBER}
                """
              }              
            }
          } //end of stage
      
      stage("Trivy Docker scan") {
            steps {
              sh """ trivy image ${nexus_url}:8082/appoint-api:${BUILD_NUMBER} """             
            }
          } //end of stage
      
     
   }
}
