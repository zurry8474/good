pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    } 
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
                    timeout(time: 120, unit: 'SECONDS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                
                }   
            }
            
        }
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                        sh '''
                           docker build -t 34.125.149.136:8083/springapp:${VERSION} .
                           docker login -u admin -p $docker_password 34.150.239.137:8083
                           docker push 34.125.149.136:8083/springapp:${VERSION}
                           docker rmi 34.125.149.136:8083/springapp:${VERSION}
                        '''
                    
                    }
                    

                }
            }
        }
    }
}