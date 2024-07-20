pipeline {
  agent any

  stages {
    stage('Build Image') {
      steps {
        script {
          dockerapp = docker.build("shmullll/cicd-jenkins:${env.BUILD_ID}", "-f ./src/Dockerfile ./src")
        }
      }
    }

    stage('Push Image') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            dockerapp.push("latest")
            dockerapp.push("${env.BUILD_ID}")
          }
        }
      }
    }

    stage('Deploy') {
      environment {
        tag_version = "${env.BUILD_ID}"
      }
      steps {
        script {
          // Configurar o aws cli
          // withAws(credentials: 'jenkins-credential', region: 'us-east-1') {
          //   sh 'aws eks --region us-east-1 update-kubeconfig --name eks-cluster'
          //   sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml'
          //   sh 'kubectl apply -f ./k8s/deployment.yaml'
          // }
          sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/deployment.yaml' // para substituir a tag no arquivo deployment.yaml
          sh 'kubectl apply -f ./k8s/deployment.yaml' // para aplicar o deployment no k8s local

        }
      }
    }


  }
}