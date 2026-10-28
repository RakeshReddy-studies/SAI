pipeline {
    agent any
    triggers {
        githubPush()
    }
    environment {
        IMAGE = 'YOUR_DOCKERHUB_USER/demo-web'
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred',
                                 usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                        docker build -t ${IMAGE}:${BUILD_NUMBER} -t ${IMAGE}:latest .
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker push ${IMAGE}:${BUILD_NUMBER}
                        docker push ${IMAGE}:latest
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f k8s/
                        kubectl set image deployment/demo-web web=${IMAGE}:${BUILD_NUMBER}
                        kubectl rollout status deployment/demo-web --timeout=180s
                    '''
                }
            }
        }
    }
    post {
        success { echo 'Pipeline finished successfully.' }
        failure { echo 'Pipeline failed - check the console output.' }
    }
}
