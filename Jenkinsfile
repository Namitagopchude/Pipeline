pipeline {
   agent any
   
   environment {
        JIRA_SITE = 'jenkins-jira'  // Make sure this matches the "Jira Site" configured in Jenkins
    }

  stages {
        stage('chheckout code') {
            steps{
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
                bat 'mvn sonar:sonar -Dsonar.projectKey=Jenkins-maven -Dsonar.projectName="Jenkins-maven" -Dsonar.token=%Sonarqube%'
              }
            }
            
        }
        stage('Archive HTML Reports') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'target/site/jacoco',
                    reportFiles: 'index.html',
                    reportName: 'Code Coverage'
                ])
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar'
            }
        
        }
        
        stage('deploy') {
            steps {
               
                bat 'java -jar "%WORKSPACE%/target/my-app-1.0-SNAPSHOT.jar"'
                 
            }
        }
        stage('Update Jira') {
            steps {
                script {
                    jiraAddComment(
                        idOrKey: 'JEN-1',
                        comment: '✅ Build completed successfully.',
                        site: env.JIRA_SITE,
                        failOnError: false
                    )
                }
            }
        }
    }

    post {
        always{
            slackSend channel: '#all-jenkinsnotifier', message: "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
    }

}
