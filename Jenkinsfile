
pipeline {
  environment {
    registry = "vincentenyinwa/lbg-vat-docker-img"
    registryCredential = "dockerhub_id"
    dockerImage = ""
    RUNSERVER = credentials('DOCKER_RUN_SERVER')
  }
  agent any
  stages {
    stage("Build DOcker Image"){
      steps {
        script {
          dockerImage=docker.build(registry)
        }
      }
    }
    
    
    stage ("push to docker hub") {
      steps {
        script {
          docker.withRegistry('', registryCredential) {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }

    stage("clean up"){
      steps{
        script{
          sh 'docker image prune --all --force --filter "until=48h"'
        }
      }
    }

    // stage("copy compose file to runserver"){
    //   steps {
    //     script {
    //       sh "sudo su -jenkins"
    //       sh "sftp $RUNSERVER"
    //       sh "put docker-compose.yaml"
    //       sh "exit"
    //     }
    //   }
    // }

    stage ("connect to runserver and run dockerfile") {
      steps {
        script {
          sh "ssh $RUNSERVER"
          sh "docker run -d -p 3306:3000 ${registry}"
        }
      }
    }
  }
}
