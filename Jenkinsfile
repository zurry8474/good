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
                    timeout(time: 360, unit: 'SECONDS') {
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
                    withCredentials([string(credentialsId: 'sonar_pass', variable: 'sonar_password')]){
                            sh '''
                                docker build -t 34.125.5.182:8083/webapp:${VERSION} .
                                docker login -u admin -p $sonar_password 34.125.5.182:8083
                                docker push  34.125.5.182:8083/webapp:${VERSION}
                                docker rmi 34.125.5.182:8083/webapp:${VERSION}
                            '''
                    
                    }
                    
                }
            }
        }
        stage('identifying misconfigs using datree in helm charts'){
            steps{
                script{
                    dir('kubernetes/') {
                        withEnv(['DATREE_TOKEN=PDQGVzku3EgoQDXPi36FCW']){
                                sh 'helm datree test myapp/'
                                
                        }
                        
                    }
                }
            }
        }
    }
}