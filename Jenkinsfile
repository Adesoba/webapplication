pipeline {
  agent any
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Init') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    
     stage ('Check-Secrets-Git') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/Adesoba/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
   
     stage ('Source-Composition-Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
   
   stage ('Static-Application-Security-Testing') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          sh 'cat target/sonar/report-task.txt'
        }
      }
    }
   
   stage ('Build-App') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Server_Tomcat') {
            steps {
            sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@3.138.69.225:/prod/apache-tomcat-8.5.69/webapps/webapp.war'
               }
            }   
    }
 
   stage ('Dynamic-Application-Security-Testing') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.232.158.44 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.232.202.25:8080/webapp/" || true'
        }
      }
    }
  }
}
