/* groovylint-disable CompileStatic, DuplicateStringLiteral, LineLength, NestedBlockDepth, NglParseError */
pipeline {
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
    }
    agent {
        label 'centos_node'
    }
    tools {
            maven 'Maven'
            jdk 'JDK 11'
    }

    stages {
        //  stage('Docker Build') {
        //     steps {
        //         script {
        //             docker.build("sportsclub-docker-local/sportsclub:${TAG}")
        //         }
        //     }
        // }

        stage('validate') {
            steps {
                echo 'VALIDATE'
                sh 'mvn clean validate'
            }
        }
        stage('Compile') {
            steps {
                echo 'COMPILE'
                sh 'mvn clean install'
            }
        }

        stage('test') {
            steps {
                echo 'Test'
                sh 'mvn clean test'
            }
        }

        stage('Sonar Analysis') {
            steps {
                sh 'mvn clean install'
                sh 'mvn sonar:sonar -Dsonar.token=cbf4cb8304fee53bde54f1d6a2273f35b5afe9fd'
            }
        }

        stage('package') {
            steps {
                echo 'Pakage'
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    docker.build("abhi_docker/sportsclub:${TAG}")
                }
            }
        }

        stage('Pushing Docker Image to Jfrog Artifactory') {
            steps {
                script {
                    docker.withRegistry('http://172.27.59.80:8082/', 'artifactory-docker') {
                        docker.image("abhi_docker/sportsclub:${TAG}").push()
                        docker.image("abhi_docker/sportsclub:${TAG}").push('latest')
                    }
                }
            }
        }
        // stage('Initialize') {
        //     steps {
        //         echo "PATH = ${M2_HOME}/bin:${PATH}"
        //         echo 'M2_HOME = /opt/maven'
        //     }
        // }

        /* stage('Deploy') {
            steps {
              sh 'docker stop sportsclub | true'

              sh 'docker rm sportsclub | true'

               sh "docker run --name sportsclub -d -p 8082:8080 http://localhost:8082/artifactory/sportsclub-docker-local/:${TAG}"
            }
       }*/

        // stage('Upload_Artifact') {
        //     steps {
        //         script {
        //             def server = Artifactory.server 'artifactory'
        //             def uploadSpec = '''{
        //           "files": [
        //             {
        //               "pattern": "target/*.jar",
        //               "target": "CI_Poc_Abhijeet/"
        //             }
        //          ]
        //         }'''
        //             server.upload(uploadSpec)
        //         }
       //     }
    }
}
