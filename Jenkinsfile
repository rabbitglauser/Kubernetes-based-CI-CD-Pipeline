pipeline {
    agent any
    environment {
        REGISTRY = 'your-registry-url'
        REGISTRY_CREDENTIALS_ID = 'registry-credentials-id'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-credentials-id'
        IMAGE_NAME = 'your-app-name'
    }
    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("${REGISTRY}/${IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY}", "${REGISTRY_CREDENTIALS_ID}") {
                        docker.image("${REGISTRY}/${IMAGE_NAME}:${env.BUILD_ID}").push()
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                        sh "kubectl apply -f k8s/deployment.yaml --kubeconfig=$KUBECONFIG"
                    }
                }
            }
        }
    }
}
