pipeline {
  agent any

  environment {
    DOCKERHUB_CRED = 'shatakshi2609'       // Jenkins ID for Docker Hub credentials
    KUBECONFIG_FILE = 'kubeconfig-file'    // Jenkins ID for kubeconfig file
    IMAGE_NAME = 'shatakshi2609/portfolio' // Docker Hub repo
    IMAGE_TAG = "${env.BUILD_NUMBER}"       // unique image tag per build
  }

  stages {
    stage('Checkout') {
      steps { 
        checkout scm 
      }
    }

    stage('Build Docker Image') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CRED}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
          sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
          sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
          sh "docker push ${IMAGE_NAME}:latest"
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: "${KUBECONFIG_FILE}", variable: 'KUBE_FILE')]) {
          sh 'mkdir -p $HOME/.kube'
          sh 'cp $KUBE_FILE $HOME/.kube/config'
          // rolling update with new image
          sh "kubectl set image deployment/portfolio-deployment portfolio=${IMAGE_NAME}:${IMAGE_TAG} --record || true"
          // apply manifests if missing
          sh "kubectl apply -f k8s/deployment.yaml || true"
          sh "kubectl apply -f k8s/service.yaml || true"
        }
      }
    }
  }
}

