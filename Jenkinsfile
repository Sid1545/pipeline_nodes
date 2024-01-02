pipeline {
    agent none
    tools {
  maven 'maven'
}
    stages {
        stage ('clone job') {
            agent { label 'master' }
            steps {
              git 'https://github.com/Sid1545/webapp.git'
            }
        }
        stage ('clone job1') {
            agent { label 'sonar' }
            steps {
              git 'https://github.com/Sid1545/webapp.git'
            }
        }
        stage ('clone job2') {
            agent { label 'jfrog' }
            steps {
              git 'https://github.com/Sid1545/webapp.git'
            }
        }
        stage ('build') {
            agent { label 'master' }
            steps {
              sh 'mvn clean install'
            }
        }
        stage ('deploy') { 
            agent { label 'master' }
            steps {
              sshagent(['tom']) {
              sh 'scp -o StrictHostKeyChecking=no /home/tomcat/workspace/pipe/target/app.war tomcat@3.238.33.75:/home/tomcat/apache-tomcat-9.0.84/webapps'
              }
            }
        }
        stage ("build & SonarQube analysis") {
            agent { label 'sonar' }
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('JFrog') {
            agent { label 'jfrog' }
            steps {
                script {
                    def server = Artifactory.server 'jfrog'
                    def uploadSpec = '''{
                        "files": [
                            {
                                "pattern": "http://44.203.34.114:8081/artifactory",
                                "target": "sid-snap/"
                            }
                        ]
                    }'''
                    server.upload(uploadSpec)
                }
            }
        }
    }
}
