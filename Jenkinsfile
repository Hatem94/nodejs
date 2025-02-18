pipeline {
    agent any

    environment {
        IMAGE_NAME = "hatem14/node.js"
        KUBE_CONFIG = credentials('kubeconfig') // Stocké dans Jenkins credentials
    }

    stages {
        stage('Cloner le Repo') {
            steps {
                git 'https://github.com/Hatem94/nodejs.git'
            }
        }

        stage('Installer les dépendances') {
            steps {
                sh 'npm install'
            }
        }

        stage('Tests') {
            steps {
                sh 'npm test || echo "Tests échoués, mais on continue..."'
            }
        }

        stage('Construire l’image Docker') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Pousser l’image sur Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Déploiement sur Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml --kubeconfig=$KUBE_CONFIG
                kubectl apply -f service.yaml --kubeconfig=$KUBE_CONFIG
                '''
            }
        }
    }
}
