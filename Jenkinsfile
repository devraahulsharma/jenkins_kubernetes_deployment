pipeline {
  environment {
    dockerimagename = "raahulsharma96/python-app"
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
    stage('Push image') {
        steps{
            script {
                withCredentials([usernamePassword( credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
                    def registry_url = "registry.hub.docker.com/"
                    sh "docker login -u $USER -p $PASSWORD ${registry_url}"
                    docker.withRegistry("https://${registry_url}", "dockerhub-credentials") {
                        // Push your image now
                        dockerImage.push("latest")
                    }
                }
            }
        }
    }
    stage('Deploying Python container to Kubernetes') {
      steps {
        script {
          // Apply deployment YAML first
          sh 'kubectl apply -f deployment.yaml'

          // Apply service YAML next
          sh 'kubectl apply -f service.yaml'
        }
      }
    }


  }
}
