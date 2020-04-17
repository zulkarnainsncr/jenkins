pipeline {
   tools {
      // Zulkarna Install the Maven version configured as "M3" and add it to the path.
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
              sh 'sudo scp -i ~kagalwalla_zul/.ssh/id_rsa /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/gameoflife.war /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile kagalwalla_zul@34.70.37.156:.'
              sh 'sudo scp -i ~kagalwalla_zul/.ssh/id_rsa /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/gameoflife.war /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/dockerfile kagalwalla_zul@35.232.207.143:.'
              sh 'sudo docker build -t mytomcat:$BUILD_NUMBER /var/lib/jenkins/workspace/gol-fromjenkinsfile/jenkinsdockerimg/'
              sh 'sudo ssh -i ~kagalwalla_zul/.ssh/id_rsa kagalwalla_zul@34.70.37.156 sudo docker build -t mytomcat:$BUILD_NUMBER'
              sh 'sudo ssh -i ~kagalwalla_zul/.ssh/id_rsa kagalwalla_zul@35.232.207.143 sudo docker build -t mytomcat:$BUILD_NUMBER'
              sh 'sudo docker service create --name webtom$BUILD_NUMBER -p 8003:8080 mytomcat:$BUILD_NUMBER'
              sh 'sudo docker service scale webtom$BUILD_NUMBER=5'
          }
      }
   }
}
