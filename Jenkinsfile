pipeline{
   agent any
   tools{
      maven 'maven'
      jdk 'JDK11'
   }
  stages {
      stage('Maven Build'){
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
     stage('Build & SonarQube analysis') {
            agent any
            steps {
                 withSonarQubeEnv('sonar-server') {
                 sh 'java -version'
                 sh 'mvn sonar:sonar'
              }
            }
          }
     stage('Quality gate') {
            steps {
                waitForQualityGate abortPipeline: true
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
     stage('Download to Artifacts'){
        steps{
        rtDownload(
         serverId : 'ARTIFACTORY_SERVER',
         spec :'''{
           "files" :[
           {
           "pattern": "artifactory-docker-dev-local-cichallenge/",
           "target": "artifacts/"
           }
           ]
         }''',
      )
      }
     }
     stage('Deploy to AWS'){
        steps{
             sshagent(['a70a80fb-a4ac-42c3-baf8-54ce6b9e1e53']){
             sh 'scp -r /var/jenkins_home/workspace/CIChallenge-Pipeline/artifacts/*.jar ubuntu@52.66.147.29:/home/ubuntu/artifacts'
         }
     }
    }
  }
   post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
            mail bcc: '', body: 'deploying is sucesfull', cc: '', from: '', replyTo: '', subject: 'deployed', to: 'karthigavj8599@gmail.com'
         }  
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: 'karthigavj8599@gmail.com', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "karthigavj8599@gmail.com";  
         }  
         unstable {  
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'  
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
  }
