pipeline {
    agent any
    tools {
         maven "Maven 3.6.3"
        jdk "JDK 11"
    }         
      
      stages {

        stage('Initialize'){
            steps{
                echo "PATH = ${M2_HOME}/bin:${PATH}"
                echo "M2_HOME = /opt/maven"
            }
        }
          
        stage('test'){
            steps{
                echo "Test"
             bat "mvn clean test"
            }
        }
          
        stage('Sonar Analysis') {
            steps {
            
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn sonar:sonar'
                }
            }
        }
          
        stage('Compile'){
            steps{
                echo "COMPILE"
             bat "mvn clean install"
            }
        }
          
        stage('Upload_Artifact') {
            steps {
                script{
               def server = Artifactory.server 'artifactory'                
               def uploadSpec = """{
                  "files": [
                    {
                      "pattern": "target/*.jar",
                      "target": "CI_Poc_Abhijeet/"
                    }
                 ]
                }"""
                server.upload(uploadSpec) 
            }
            }
        }
    }
    
}
