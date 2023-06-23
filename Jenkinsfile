/* groovylint-disable CompileStatic, DuplicateStringLiteral, LineLength, NestedBlockDepth, NglParseError */
pipeline {
    environment {
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
    }
    agent {
        label 'Abhijeet'
    }
    tools {
            maven 'Maven'
            jdk 'JDK11'
    }

    stages {
        stage('validate') {
            steps {
                echo 'VALIDATE'
                sh 'mvn clean validate'
            }
        }
        stage('Compile') {
            steps {
                echo 'COMPILE'
                bat 'mvn clean compile'
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
                bat 'mvn clean install'
                bat 'mvn sonar:sonar -Dsonar.token=cbf4cb8304fee53bde54f1d6a2273f35b5afe9fd'
            }
        }

        stage('package') {
            steps {
                echo 'Pakage'
                bat 'mvn clean package'
            }
        }
        stage('Docker Build') {
            when {
                branch 'release'
            }
            steps {
                script {
                    docker.build("abhi_docker/sportsclub:${TAG}")
                }
            }
        }

        stage('Pushing Docker Image to Jfrog Artifactory') {
            when {
                branch 'release'
            }
            steps {
                script {
                    docker.withRegistry('http://172.27.59.80:8082/', 'artifactory-docker') {
                        docker.image("abhi_docker/sportsclub:${TAG}").push()
                        docker.image("abhi_docker/sportsclub:${TAG}").push('latest')
                    }
                }
            }
        }
        stage('Deploy'){
            when {
                branch 'release'
            }
            steps {
                sh 'docker-compose up'
            }
        }
    }
}
