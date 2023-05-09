/* groovylint-disable CompileStatic */
pipeline {
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
    }
    agent centos_node
    tools {
            maven 'Maven 3.6.3'
            jdk 'JDK 11'
    }
    stages {
        /* stage('Docker Build') {
            steps {
                script {
                    docker.build("sportsclub-docker-local/sportsclub:${TAG}")
                }
            }
        }

        stage('Pushing Docker Image to Jfrog Artifactory') {
            steps {
                script {
                    /* groovylint-disable-next-line NestedBlockDepth *
                    docker.withRegistry('http://localhost:8082/', 'Jfrog-jenkinsUserPassword') {
                        docker.image("sportsclub-docker-local/sportsclub:${TAG}").push()
                        docker.image("sportsclub-docker-local/sportsclub:${TAG}").push('latest')
                    }
                }
            }
        }*/

        stage('Initialize') {
            steps {
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo 'M2_HOME = /opt/maven'
            }
        }

        stage('test') {
            steps {
                echo 'Test'
                bat 'mvn clean test'
            }
        }

        stage('Sonar Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn sonar:sonar'
                }
            }
        }

        stage('Compile') {
            steps {
                echo 'COMPILE'
                bat 'mvn clean install'
            }
        }

        /* stage('Deploy') {
            steps {
               bat 'docker stop sportsclub | true'

               bat 'docker rm sportsclub | true'

                bat "docker run --name sportsclub -d -p 8082:8080 http://localhost:8082/artifactory/sportsclub-docker-local/:${TAG}"
            }
       }*/

        stage('Upload_Artifact') {
            steps {
                script {
                    def server = Artifactory.server 'artifactory'
                    def uploadSpec = '''{
                  "files": [
                    {
                      "pattern": "target/*.jar",
                      "target": "CI_Poc_Abhijeet/"
                    }
                 ]
                }'''
                    server.upload(uploadSpec)
                }
            }
        }
    }
}

