pipeline {

// agent { node { label 'server IP' } }

/ agent none

options {
    skipDefaultCheckout()
    disableConcurrentBuilds()
    disableResume()
 }
 
/* tools {
         
        maven 'apache-maven-4.0.0'
      //  ant 'apache-ant-1.8.2'

}*/


stages{

    // Checkout stage (code checkout from scm tool)

    stage('checkout from GIT') {
        steps {
            checkout scm
        }
    }


    
    // Installing Dependancies   
     stage("Install Project Dependencies"){
     steps {
         sh 'mvn install'
          }
      }

     // Building    
     stage("Build Project"){
     steps { 
        sh 'mvn package' 
        sh 'mvn clean'
     }
     }


    // Code quality checks
     stage('Run quality checks') {
         when {
             allOf {
                 branch 'branchname'
             }
         }
         environment {
             scannerHome = tool 'SonarQubeScanner'
         }
         steps {
             withSonarQubeEnv('sonarqube') {
                 sh '${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=reponame -Dsonar.sources=src'
             }
         }
     }
    
   //  Code quality gate checks
     stage ("SonarQube Quality Gate") {
         when {
             allOf {
                 branch 'branchname'
             }
         }
         steps {
             script {
                 def qualitygate = waitForQualityGate() 
                 if (qualitygate.status != "OK") {
                     error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                 }
             }
         }
     }

   // Create Container and push to Container registry
    stage('Docker Build and Push to hub') {
    
        steps {
            echo "Building phase started"
            script{
                docker.withRegistry('url of docker repo', 'Credentials ID') {
                    def customImage = docker.build("url of docker repo/env name/apiname:${currentBuild.id}")
                    customImage.push("${currentBuild.id}")  
                }
            }
        }
    }    


}
}
