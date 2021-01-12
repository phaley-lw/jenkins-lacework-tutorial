pipeline {
  agent any
  stages {
    stage('Build Docker Image') {
      when {
        branch 'master'
      }
      steps {
        script {
          app = docker.build("$DOCKER_HUB/lacework-cli")
          app.inside {
            sh 'lacework --help'
          }
        }

      }
    }

    stage('Push Docker Image') {
      when {
        branch 'master'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'docker_hub') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
          }
        }

      }
    }

    stage('Lacework Vulnerability Scan') {
      agent {
        docker {
          image 'techallylw/lacework-cli:latest'
        }

      }
      when {
        branch 'master'
      }
      environment {
        LW_API_SECRET = credentials('lacework_api_secret')
      }
      steps {
        echo 'Running Lacework vulnerability scan'
        sh "lacework vulnerability container scan index.docker.io pthaley/lacework-cli latest --poll --noninteractive --details"
      }
    }

  }
}