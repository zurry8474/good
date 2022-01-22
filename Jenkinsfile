pipeline{
    agent any
    environment{
        VERSION="${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }   
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonatype') {
                             sh 'chmod +x gradlew'
                             sh './gradlew sonarqube'          
                    }    
                }
            }
        stage ("Docker build"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
    
                            sh '''
                               docker build -t 34.125.46.97:8083/benspringapp:${VERSION} .
                               docker docker login -u admin -p $docker_password 34.125.46.97:8083 
                               docker push 34.125.46.97:8083/benspringapp:${VERSION}
                               docker rmi 34.125.46.97:8083/benspringapp:${VERSION}
                            '''
                    }
                }
            }
        }
        
        }       
    }
}
