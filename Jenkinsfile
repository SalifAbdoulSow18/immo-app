pipeline {
    agent any
    
    environment {
        GITHUB_USERNAME = 'SalifAbdoulSow18'
        DOCKER_HUB_USERNAME = 'sasow'
        DOCKER_HUB_REPO = 'immo-app'
        IMAGE_NAME = "${DOCKER_HUB_USERNAME}/${DOCKER_HUB_REPO}"
        K8S_NAMESPACE = 'immo-app'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo "📦 Récupération du code depuis GitHub..."
                // ⚠️ Ne pas utiliser de variable ici, mettre l'URL complète
                git branch: 'main', 
                    url: 'https://github.com/SalifAbdoulSow18/immo-app.git',  // ← URL complète
                    credentialsId: 'github-credentials'
                echo "✅ Code récupéré"
            }
        }
        
        stage('Read Version') {
            steps {
                script {
                    def versionFile = readFile('VERSION').trim()
                    env.APP_VERSION = versionFile
                    echo "📌 Version: ${env.APP_VERSION}"
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo "🐳 Build de l'image Docker..."
                script {
                    sh """
                        docker build -t ${IMAGE_NAME}:${env.APP_VERSION} .
                        docker tag ${IMAGE_NAME}:${env.APP_VERSION} ${IMAGE_NAME}:latest
                        docker tag ${IMAGE_NAME}:${env.APP_VERSION} ${IMAGE_NAME}:${env.GIT_COMMIT}
                    """
                }
                echo "✅ Image buildée: ${IMAGE_NAME}:${env.APP_VERSION}"
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo "📤 Push vers Docker Hub..."
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASS')]) {
                    sh """
                        echo "${DOCKER_PASS}" | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin
                        docker push ${IMAGE_NAME}:${env.APP_VERSION}
                        docker push ${IMAGE_NAME}:latest
                        docker push ${IMAGE_NAME}:${env.GIT_COMMIT}
                        docker logout
                    """
                }
                echo "✅ Image poussée sur Docker Hub"
            }
        }
        
        stage('Update Git Manifests') {
            steps {
                echo "📝 Mise à jour du tag dans les manifests..."
                script {
                    // Pour le push, on peut utiliser la variable avec le token
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        sh """
                            # Mettre à jour l'image dans deployment.yaml
                            sed -i "s|image: ${IMAGE_NAME}:.*|image: ${IMAGE_NAME}:${env.APP_VERSION}|" k8s/deployment.yaml
                            
                            # Commit et push
                            git config user.email "jenkins@immoapp.com"
                            git config user.name "Jenkins CI"
                            git add k8s/deployment.yaml
                            git commit -m "release: bump image to ${IMAGE_NAME}:${env.APP_VERSION} [skip ci]" || echo "No changes"
                            git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/${GITHUB_USERNAME}/immo-app.git main
                        """
                    }
                }
                echo "✅ Manifests mis à jour sur GitHub"
            }
        }
        
        stage('Create GitHub Release') {
            steps {
                echo "🏷️ Création d'une release GitHub..."
                withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                    sh """
                        git tag -a ${env.APP_VERSION} -m "Release ${env.APP_VERSION}"
                        git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/${GITHUB_USERNAME}/immo-app.git ${env.APP_VERSION}
                    """
                }
                echo "✅ Release GitHub créée: ${env.APP_VERSION}"
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
                
                📦 Version: ${env.APP_VERSION}
                🐳 Image: ${IMAGE_NAME}:${env.APP_VERSION}
                
                🌐 Application: http://${IP}:30080
                
                ════════════════════════════════════════════════════════════════
                """
            }
        }
        failure {
            echo "❌ PIPELINE ÉCHOUÉ - Vérifiez les logs"
        }
    }
}