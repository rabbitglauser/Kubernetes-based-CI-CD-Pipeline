pipeline {
    agent any
    environment {
        REGISTRY = '*********************'
        REGISTRY_CREDENTIALS_ID = '**************'
        KUBECONFIG_CREDENTIALS_ID = '*********'
        IMAGE_NAME = 'Pipeline'
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
