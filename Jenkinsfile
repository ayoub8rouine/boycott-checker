pipeline {
    agent any

    stages {

        stage('Checkout code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/ayoub8rouine/boycott-checker.git',
                    credentialsId: 'right'  // Uuse youhr GitHub PAT credential ID
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test code') {
            steps {
                sh 'npm test -- --watch=false --browsers=ChromeHeadlessCI'
            }
        }

        stage('Build app') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dokcer-paswd', variable: 'dockerhubpwd')]) {
                        // Login to DockerHub
                        sh 'echo "${dockerhubpwd}" | docker login -u poldiro --password-stdin'
                        
                        // Build image locally, tag for push, then push
                        sh 'docker build -t consume-safe:${BUILD_NUMBER} .'
                        sh 'docker tag consume-safe:${BUILD_NUMBER} poldiro/consume-safe:${BUILD_NUMBER}'
                        sh 'docker push poldiro/consume-safe:${BUILD_NUMBER}'
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Replace image tag dynamically with current Jenkins build number
                    sh "sed -i 's|consume-safe:.*|poldiro/consume-safe:${BUILD_NUMBER}|' k8s/deployment.yaml"
                    
                    withKubeConfig(credentialsId: 'kube-configfile', serverUrl: 'https://kubernetes.docker.internal:6443') {
                        // Apply deployment and service
                        sh 'kubectl apply -f k8s/'
                        
                        // Check pods status
                        sh 'kubectl get pods -o wide'
                    }
                }
            }
        }

    }

    post {
        success {
            echo '✅ Build, push, and deployment completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Please check the logs.'
        }
    }
}

