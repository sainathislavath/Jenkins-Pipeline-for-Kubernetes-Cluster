pipeline {
  agent any

  environment {
    AWS_REGION = 'us-west-2'
    EKS_CLUSTER_NAME = 'e-commerce-deployment'
  }

  stages {

    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/sainathislavath/Jenkins-Pipeline-for-Kubernetes-Cluster.git', branch: 'main'
      }
    }

    stage('Build and Push Docker Images') {
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {

            def services = [
              'user-service',
              'product-service',
              'cart-service',
              'order-service'
            ]

            services.each { service ->
              dir("backend/${service}") {
                sh """
                  docker build -t ${DOCKERHUB_USER}/${service}:latest .
                  echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                  docker push ${DOCKERHUB_USER}/${service}:latest
                """
              }
            }

            dir("frontend") {
              sh """
                docker build -t ${DOCKERHUB_USER}/e-commerce-frontend:latest .
                echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                docker push ${DOCKERHUB_USER}/e-commerce-frontend:latest
              """
            }
          }
        }
      }
    }

    stage('Update Kubeconfig') {
      steps {
        sh "aws eks --region ${AWS_REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}"
      }
    }

    stage('Verify AWS Identity') { // Optional
      steps {
        sh "aws sts get-caller-identity"
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh "kubectl apply -f k8s/"
      }
    }
  }

  post {
    success {
      echo "✅ Deployment to EKS successful!"
    }
    failure {
      echo "❌ Deployment failed. Check logs."
    }
  }
}
