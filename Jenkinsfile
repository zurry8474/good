pipeline {
    agent any 
    stages{
        stage("sonarqube static code check"){
            agent {
                docker{
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId:'sonarpass') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
                
                
                }   
            }
            
        }
    }
}