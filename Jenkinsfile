pipeline {
    agent any

    stages {
      stage('Pull Docker Image from DockerHub') {
            steps{
                script {
               docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
               image = docker.image('dmitrykzn/sborshik2:v2')
               image.pull() 
                 }
               }
            }
        }
   
      stage('Run Docker container from image') {
          agent { 
            docker {
              image 'registry.hub.docker.com/dmitrykzn/sborshik2:v2'
              args '-u root:sudo -v /var/run/docker.sock:/var/run/docker.sock'
            }
          }   
        //stages inside Docker container *sborshik1*
        stages {
          stage ('Clone app from GitHub') {  
            steps {
                git 'https://github.com/dmitry-kzn/boxfuse-sample-java-war-hello.git'
            }
          }
      
          stage ('Build war by Maven') {
            steps {
              sh "\"/opt/apache-maven-3.6.3/bin/mvn\" package" 
            }
          }
          stage ('Make docker image and tag it') {
            steps {
              git 'https://github.com/dmitry-kzn/Lesson11-Dockerfile_to_package_war.git'
              sh 'cp Dockerfile ./target/ && cd target/ && ls && docker build -t war_with_apache .'
              sh 'docker image tag war_with_apache dmitrykzn/war_with_apache_2:v2'
            }
          }
          stage ('Push Docker image to GitHub') {
            steps {
              script {
               docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
               image = docker.image('dmitrykzn/war_with_apache_2:v2')
               image.push()
               }
              }
            }
          }
        }
        ///////////////////////////////////////
      }
    }      
}  
