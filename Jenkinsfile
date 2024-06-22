pipeline {
  environment {
    dockerimagename = "raahulsharma96/python-app"
    dockerImage = ""
    KUBECONFIG_PATH = "/home/jenkins/.kube/config" // Path to kubeconfig in Minikube
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/raahulsharm96/jenkins_kubernetes_deployment.git'
      }
    }
    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Push image') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
            def registry_url = "registry.hub.docker.com/"
            sh "docker login -u $USER -p $PASSWORD ${registry_url}"
            docker.withRegistry("https://${registry_url}", "dockerhub-credentials") {
              dockerImage.push("latest")
            }
          }
        }
      }
    }
    stage('Install kubectl') {
      steps {
        script {
          sh '''
          if ! command -v kubectl &> /dev/null
          then
            curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
            chmod +x kubectl
            mkdir -p /var/jenkins_home/bin
            mv kubectl /var/jenkins_home/bin/
          fi
          '''
        }
      }
    }
    // stage('Configure kubectl') {
    //   steps {
    //     script {
    //       // Copy the kubeconfig from Minikube to the Jenkins workspace
    //       sh '''
    //       mkdir -p /home/jenkins/.kube
    //       cp /root/.kube/config /home/jenkins/.kube/config
    //       '''
    //     }
    //   }
    // }
    // stage('Deploying Python container to Kubernetes') {
    //   steps {
    //     script {
    //       sh '''
    //       export PATH=/var/jenkins_home/bin:$PATH
    //       export KUBECONFIG=/home/jenkins/.kube/config
    //       kubectl apply -f deployment.yaml
    //       kubectl apply -f service.yaml
    //       '''
    //     }
    //   }
    // }
    stage('Deploying container to Kubernetes') {
      steps {
        script {
          // kubernetesDeploy(configs: "deployment.yaml", kubeconfigId:"kubernetes")
          kubernetesDeploy(configs: "deployment.yaml" kubeconfig:"config.yaml")

        }
      }
    }
  }
}
