pipeline {
   tools {
      // Zul Install the Maven version configured as "M3" and add it to the path.
      maven "mymaven"
   }
   agent none

   stages {
      stage('Build') {
          agent any
          steps {
             git 'https://github.com/wakaleo/game-of-life.git' 
          }
      }
      stage('compile') {
          agent any
          steps {
              sh 'mvn compile'
          }
      }
      stage('test') {
          agent any
          steps {
              sh 'mvn test'
          }
          post {
              always {
                  junit 'gameoflife-web/target/surefire-reports/*.xml'
              }
          }
      }
      stage('package') {
          agent any
          steps {
              sh 'mvn package'
          }
      }
      stage('deploy') {
          agent any
          steps {
              sh 'rm -rf /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg'
              sh 'mkdir /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg'
              sh 'cp /var/lib/jenkins/workspace/gol-fromjenkinsfile/gameoflife-web/target/gameoflife.war /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/.'
              sh 'touch /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile'
              sh 'echo "From tomcat" >> /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile'
              sh 'echo "ADD gameoflife.war /usr/local/tomcat/webapps" >> /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile'
              sh 'echo "EXPOSE 8080" >> /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile'
              sh 'sudo docker build -t mytomcat:$BUILD_NUMBER /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/'
              sh 'sudo docker run  -itd -P mytomcat:$BUILD_NUMBER'
          }
      }
   }
}
