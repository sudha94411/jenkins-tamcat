pipeline {
    agent any 
    stages {
        stage ("GitCheckOut"){
          steps {
              git credentialsId: 'sudharani', url: 'https://github.com/sudha94411/sudharani.git'
          }
        }
        stage ("Maven Build"){
          steps {
                sh "mvn clean package"
           }
         }
         stage ("deploy-dev"){
             steps {
                 sshagent(['tomcat-new']) {
            sh """
                 scp -o StringHostKeyChecking=no target/myweb ec2-user@172.31.49.93:home/ec2-user/apache-tomcat-9.0.65/webapps/
                 ssh ec2-user@172.31.49.93 /home/ec2-user/apache-tomcat-9.0.65/bin/shutdown.sh
                 ssh ec2-user@172.31.49.93 /home/ec2-user/apache-tomcat-9.0.65/bin/startup.sh
                 
               """
                 }
             }
         }
    }
}

