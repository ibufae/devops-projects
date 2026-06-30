pipeline {
    agent any // Utilise n'importe quel agent Jenkins disponible

    stages {
        stage('Clone Project') {
            steps {
                // Clone automatiquement la branche courante dans un pipeline multibranch
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Construction de l'image Docker pour le backend
                sh 'docker build -t moodboard-backend-app:1.0.0 moodboard-backend'

                // Construction de l'image Docker pour le frontend
                sh 'docker build -t moodboard-frontend-app:1.0.0 moodboard-frontend'
            }
        }

        stage('Tag Docker Image') {
            steps {
                // Tag des images Docker avec les noms de dépôt sur Docker Hub
                sh 'docker image tag moodboard-frontend-app:1.0.0 ibufae/moodboard-frontend-app:1.0.0'
                sh 'docker image tag moodboard-backend-app:1.0.0 ibufae/moodboard-backend-app:1.0.0'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "Pushing image to Docker Hub..."

                // Utilisation des identifiants Jenkins pour se connecter à Docker Hub
                withCredentials([usernamePassword(
                    credentialsId: "token-dockerhub-jenkins", 
                    usernameVariable: "DOCKER_USER", 
                    passwordVariable: "DOCKER_PASSWORD"
                )]) {
                    // Connexion et push des images Docker vers Docker Hub
                    sh """
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ibufae/moodboard-frontend-app:1.0.0
                        docker push ibufae/moodboard-backend-app:1.0.0
                    """
                }
            }
        }

        stage('Deploy to Environment') {
            steps {
                script {
                    // Étape conditionnelle de déploiement selon la branche
                    if (env.BRANCH_NAME == 'dev') {
                        echo 'Deploy code to DEV'
                    } else if (env.BRANCH_NAME == 'main') {
                        echo 'Deploy code to PROD'
                    } else {
                        // Pour les autres branches, aucun déploiement n'est effectué
                        echo "No deployment step for branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }
    }
}
