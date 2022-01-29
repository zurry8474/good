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
                                docker build -t 35.245.145.215:8083/webapp:${VERSION} .
                                docker login -u admin -p $docker_password 35.245.145.215:8083
                                docker push  35.245.145.215:8083/webapp:${VERSION}
                                docker rmi 35.245.145.215:8083/webapp:${VERSION}
                            '''
                    
                    }
                    
                }
            }
        }
        stage('identifying misconfigs using datree in helm charts') {
            steps{
                script{
                    dir('kubernetes/') {
                        sh 'helm datree test myapp/'
                    }
                }
            }
        }
    }
}