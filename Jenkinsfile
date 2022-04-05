pipeline {

agent { node { label 'panther' } }



options {
    skipDefaultCheckout()
    disableConcurrentBuilds()
    disableResume()
 }
 
/* tools {
         
        maven 'apache-maven-4.0.0'
        ant 'apache-ant-1.8.2'

}*/


stages{

        stage('checkout from GIT') {
        steps {
            checkout scm
        }
    }
}


    
   
     ("Install Project Dependencies")
     steps {
         sh 'mvn install'
          }
      

  
     
     steps { 
        sh 'mvn package' 
        
     }
     



     
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

