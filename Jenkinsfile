pipeline {
   agent any
   tools {
     maven 'Maven'
}    
stages {
 stage('Initialize'){
   steps {
     sh '''
               echo "PATH = ${PATH}"
               echo "M2_HOME= ${M2_HOME}"
        '''
 }
}

stage ('Check-Git-Secrets')  {
      steps {
         sh 'rm trufflehog || true'
         sh 'docker  run gesellix/trufflehog --json https://github.com/sarjit789/webapp_devsecops.git > trufflehog'
         sh 'cat trufflehog'
       }
   }
stage ('Source Composition Analysis') {
   steps {
    sh 'rm  owasp* || true'
    sh  'wget "https://raw.githubusercontent.com/sarjit789/webapp_devsecops/refs/heads/main/owasp-dependency-check.sh" '
    sh 'chmod +x owasp-dependency-check.sh'
    sh 'bash owasp-dependency-check.sh'
    sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
   }
}
stage ('Build') {
 steps {
 sh 'mvn clean package'
}
  }
stage('Deploy-To-Tomcat') {
   steps {
      sshagent(['tomcat']) {
         sh 'scp -o StrictHostKeyChecking=no target/WebApp.war ubuntu@35.89.209.95:/prod/apache-tomcat-9.0.105/webapps/WebApp.war'
      }
   }
}

   }
}
