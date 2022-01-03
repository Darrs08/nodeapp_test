pipeline {

  environment {
    dockerimagename = "darrtips4you/nodeapp"
    dockerImage = ""
    DOCKER_TAG = getDockerTag()
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/Darrs08/nodeapp_test.git'
      }
    }

    stage('Build image') {
      steps{
        sh "docker build . -t ${dockerimagename}:${DOCKER_TAG}"
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    
    stage('Pushing Image') {
      steps{
           withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u darrs08 -p ${dockerHubPwd}"
                    sh "docker push ${dockerimagename}:${DOCKER_TAG}"
            }
    }
    }
    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
        }
      }
    }
  }
}
def getDockerTag(){
    def tag = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}
