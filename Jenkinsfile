    pipeline {
        agent any

        tools {
            // Install the Maven version configured as "M3" and add it to the path.
            maven "Maven"
        }

        stages{
                stage('Clone SCM') {
                    steps {
                        git branch: 'main', credentialsId: 'ce974fa1-13fe-4145-8212-5452dce5b6f4', url: 'https://github.com/Lavakumarollipi/WebApp.git'
                        }
                    }
                stage ('build'){
                    steps {
                        sh 'mvn clean install'
                    }
                }
                stage ('Package'){
                    steps {
                        sh 'mvn clean package'
                    }
                }
                stage('SonarScanner') {
                    steps {
                        withSonarQubeEnv('Sonarqube-9.6.1'){
                        sh "mvn sonar:sonar"
                        }
                    }
                }
                stage ('Server'){
                steps {
                   rtServer (
                     id: "Jfrog-Server",
                     url: 'http://13.232.140.174:8082/artifactory',
                     username: 'jenkins',
                      password: 'Lava143@',
                      bypassProxy: true,
                       timeout: 600
                            )
                        }
                    }
                stage('Upload'){
                steps{
                    rtUpload (
                     serverId:"Jfrog-Server" ,
                      spec: '''{
                       "files": [
                          {
                          "pattern": "*.war",
                          "target": "web-app-libs-snapshot-local"
                          }
                                ]
                               }''',
                            )
                        }
                    }
                stage('Docker Build') {
                    agent any
                          steps {
                          sh 'docker build -t kumarolipi/jenkins-docker .'
                      }
                  }
                  stage('Docker Push') {
                        steps{
                            script{
                                withCredentials([string(credentialsId: 'Docker-push', variable: 'Docker-push')]) {
                                sh 'echo "Lasya143@" | docker login -u kumarolipi --password-stdin'
                            }
                                sh 'docker push kumarolipi/jenkins-docker'

                      }
                  }
               }
            }
        }