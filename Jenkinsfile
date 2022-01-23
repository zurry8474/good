pipeline {
  agent any 
    stages{
        stage("sonarqube static code check"){
            agent{
                docker{
                    image 'openjdk:11'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }

            steps{
                script{
                   withSonarQubeEnv(credentialsId: 'sonartoken') {
                       sh 'chmod +x gradlew'
                       sh './gradlew sonarqube'
                    }
                }
            }
            
        }
    }
}