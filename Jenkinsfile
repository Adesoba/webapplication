pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
   stage ('Build') {
     steps {
     sh 'mvn clean package'
   }
   }
   stage ('Deploy-To-Tomcat') {
            steps {
            sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war rpi301@rpi301:/prod/apache-tomcat-9.0.89/webapps/webapp.war'
              }      
           }       
    }
   
    
  }
}
