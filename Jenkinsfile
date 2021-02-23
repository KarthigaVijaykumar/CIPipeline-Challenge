pipeline{
   agent {
    docker {
      image 'maven:3.6.3-jdk-11'
    }
  }
  stages {
      stage("Maven Build"){
          steps{
              sh 'mvn -B -DskipTests clean package'
          }
      }
      stage('Maven Test'){
            steps{
                sh 'mvn test'
            }
            post{
            always{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
     stage("Build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar-server') {
                sh 'java -version'
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
     stage('Deploy to artifactory'){
        steps{
        rtUpload(
         serverId : 'ARTIFACTORY_SERVER',
         spec :'''{
           "files" :[
           {
           "pattern":"target/*.jar",
           "target":"artifactory-docker-dev-local-cichallenge"
           }
           ]
         }''',
         
      )
      }
     }
    }
  }
