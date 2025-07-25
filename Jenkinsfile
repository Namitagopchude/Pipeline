pipeline {
   agent any

  stages {
        stage('chheckout code') {
                steps {
                    checkout scm
                }
            
        }

        stage('build') {
            steps {
                bat 'mvn clean install -Denforcer.skip=true'
            }
        
        }

        stage('SonarQube Analysis') {

            steps {
              withCredentials([string(credentialsId: 'Sonarqube', variable: 'Sonarqube')]) {
                bat 'mvn sonar:sonar -Dsonar.projectKey=Jenkins-maven -Dsonar.projectName="Jenkins-maven" -Dsonar.token=Sonarqube'
              }
            }
            
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Archieve artifacts') {
          
            }
            post {
                success {
                archiveArtifacts artifacts: '**/target/*.jar'
                }
            }
        
        }
        
        stage('deploy') {
            steps {
               
                bat 'java -jar "%WORKSPACE%/target/my-app-1.0-SNAPSHOT.jar"'
                 
            }
        }
    }
}
