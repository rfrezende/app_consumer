pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'docker build --file consumer.Dockerfile --tag $DOCKERHUB_APP_PROJETO_ADA_IMAGE:consumer .'
      }
    }
    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
          sh 'docker push $DOCKERHUB_APP_PROJETO_ADA_IMAGE:consumer'
        }
      }
    }
    stage('Atualizar Terraform') {
        steps {
            sh 'cd /var/lib/jenkins/criar_infraestrutura && git pull'
        }
    }
    stage('Terraform Init') {
        steps {
            sh 'cd /var/lib/jenkins/criar_infraestrutura && terraform init'
        }
    }
    stage('Terraform Apply') {
      steps {
          sh 'cd /var/lib/jenkins/criar_infraestrutura && terraform apply -auto-approve'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
