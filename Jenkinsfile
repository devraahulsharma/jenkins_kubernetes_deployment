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
    stage('Push image') {
        withCredentials([usernamePassword( credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
            def registry_url = "registry.hub.docker.com/"
            bat "docker login -u $USER -p $PASSWORD ${registry_url}"
            docker.withRegistry("https://${registry_url}", "dockerhub-credentials") {
                // Push your image now
                dockerImage.push("latest")
            }
        }
    }
//     stage('Pushing Image') {
//       environment {
//           registryCredential = 'dockerhub-credentials'
//            }
//       steps{
//         script {
//           docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
//             dockerImage.push("latest")
//           }
//         }
//       }
//     }
    stage('Deploying Python container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml, service.yaml")
        }
      }
    }
  }
}
