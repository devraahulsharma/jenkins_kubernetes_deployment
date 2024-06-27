pipeline {
  environment {
    dockerimagename = "raahulsharma96/python-app"
    dockerImage = ""
    // KUBECONFIG_PATH = "/home/raahul/.kube/config" // Path to kubeconfig in Minikube
    // KUBECONFIG_PATH = "/home/raahul/.minikube/config/config.json" // Path to kubeconfig in Minikube
    // kubeconfigId : 'my-config'

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
    stage('Deploying Python container to Kubernetes') {
        steps {
            script {
                sh '''
                export PATH=/var/jenkins_home/bin:$PATH
                export KUBECONFIG=/home/raahul/.kube/config
                
                # Check if the script exists before trying to execute it
                if [ -f "/var/jenkins_home/workspace/nkins_kubernetes_deployment_main@tmp/durable-8b387b9c/script.sh.copy" ]; then
                    chmod +x /var/jenkins_home/workspace/nkins_kubernetes_deployment_main@tmp/durable-8b387b9c/script.sh.copy
                    /var/jenkins_home/workspace/nkins_kubernetes_deployment_main@tmp/durable-8b387b9c/script.sh.copy
                else
                    echo "Script not found or not generated."
                fi
                
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }

    // stage('Deploying container to Kubernetes') {
    //   steps {
    //     script {
    //       kubernetesDeploy(configs: "deployment.yaml", kubeconfigId:"my-config")
    //       // kubernetesDeploy(configs: "deployment.yaml")
    //       // sh 'kubectl apply -f deployment.yaml' // Assuming kubectl is installed and accessible
    //     }
    //   }
    // }
  }
}
