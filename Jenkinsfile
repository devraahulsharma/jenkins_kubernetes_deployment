pipeline {
  environment {
    dockerimagename = "bravinwasike/python-app"
    dockerImage = ""
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main',  url: 'https://github.com/raahulsharm96/jenkins_kubernetes_deployment.git'

      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'a149c224-d3c3-4013-a75b-f6ef5bbf85b3	'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploying Python container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml, service.yaml")
        }
      }
    }
  }
}
