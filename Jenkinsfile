pipeline{
    agent any
    stages{
        stage("sonar quality test"){
            agent{
                docker {
                    image 'openjdk:11'
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