pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USERNAME = 'sasow'
        DOCKER_HUB_REPO = 'immo-app'
        IMAGE_NAME = "${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPO}"
        K8S_NAMESPACE = 'immo-app'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "📦 Récupération du code..."
                git branch: 'main', 
                    url: 'https://github.com/SalifAbdoulSow18/immo-app.git',
                    credentialsId: 'github-credentials'
                echo "✅ Code récupéré - Commit: ${env.GIT_COMMIT}"
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "🐳 Build de l'image Docker..."
                sh """
                    docker build -t ${IMAGE_NAME}:${env.GIT_COMMIT} .
                    docker tag ${IMAGE_NAME}:${env.GIT_COMMIT} ${IMAGE_NAME}:latest
                """
                echo "✅ Image buildée: ${IMAGE_NAME}:${env.GIT_COMMIT}"
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo "📤 Push vers Docker Hub..."
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                    sh """
                        echo "${DOCKER_PASS}" | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin
                        docker push ${IMAGE_NAME}:${env.GIT_COMMIT}
                        docker push ${IMAGE_NAME}:latest
                        docker logout
                    """
                }
                echo "✅ Image poussée sur Docker Hub"
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo "☸️ Déploiement sur Kubernetes..."
                sh """
                    # Créer le namespace s'il n'existe pas
                    kubectl create namespace ${K8S_NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -
                    
                    # Appliquer les manifests
                    kubectl apply -f k8s/namespace.yaml
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    
                    # Attendre le déploiement
                    kubectl rollout status deployment/immo-app -n ${K8S_NAMESPACE} --timeout=120s
                """
                echo "✅ Déploiement terminé"
            }
        }
    }
    
    post {
        success {
            script {
                def IP = sh(script: "curl -s ifconfig.me", returnStdout: true).trim()
                echo """
                ════════════════════════════════════════════════════════════════
                ✅ PIPELINE RÉUSSI !
                ════════════════════════════════════════════════════════════════
                
                🌐 Application : http://${IP}:30080
                🌐 Jenkins : http://${IP}:8080
                🌐 ArgoCD : https://${IP}:30443
                
                ════════════════════════════════════════════════════════════════
                """
            }
        }
        failure {
            echo """
            ════════════════════════════════════════════════════════════════
            ❌ PIPELINE ÉCHOUÉ
            ════════════════════════════════════════════════════════════════
            
            Vérifiez les logs ci-dessus.
            
            Commandes de diagnostic :
            - docker images
            - docker ps
            - kubectl get pods -n immo-app
            ════════════════════════════════════════════════════════════════
            """
        }
    }
}