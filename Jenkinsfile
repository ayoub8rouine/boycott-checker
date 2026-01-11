pipeline {
  agent any
  
  tools {
    nodejs 'NodeJS-20'
  }
  
  environment {
    BUILD_TAG = "${env.GIT_COMMIT?.substring(0,8) ?: 'manual'}"
    SCANNER_HOME = tool 'MySonarQubeScanner'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Install') {
      steps { 
        sh 'npm install'
      }
    }

    stage('Lint') {
      steps { sh 'npm run lint' }
    }

    stage('Test') {
      steps { sh 'npm test -- --watch=false --browsers=ChromeHeadlessCI' }
    }

    stage('SonarQube') {
      steps {
        withSonarQubeEnv('MySonarQubeServer') {
          sh '${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=boycott-app'
        }
      }
    }

    // stage('Wait Sonar QG') {
    //   steps {
    //     timeout(time: 5, unit: 'MINUTES') {
    //       waitForQualityGate abortPipeline: true
    //     }
    //   }
    // }

    stage('Dependency Security') {
      steps {
        sh 'npm audit --audit-level=high || (echo "npm audit failed" && exit 1)'
      }
    }

    stage('Build Angular App') {
      steps {
        sh 'npm run build'
      }
    }

    stage('Build & Push Docker Image') {
      steps {
        script {
          // For local PC: Build image locally (no push to avoid credentials)
          // If you want to push, set up DockerHub creds in Jenkins
          sh 'docker build -t consume-safe:${BUILD_NUMBER} .'
          
          // Optional push (uncomment and add creds if needed)
          // withCredentials([string(credentialsId: 'dokcer-paswd', variable: 'dockerhubpwd')]) {
          //   sh 'echo "${dockerhubpwd}" | docker login -u poldiro --password-stdin'
          //   sh 'docker push poldiro/consume-safe:${BUILD_NUMBER}'
          // }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        script {
          // For local PC: Use local kubeconfig (no credentials needed if running locally)
          // Update deployment.yaml with build number
          sh "sed -i 's|consume-safe:.*|consume-safe:${BUILD_NUMBER}|' k8s/deployment.yaml"
          
          // Apply K8s files (assumes local cluster like Minikube)
          sh 'kubectl apply -f k8s/'
          
          // Check pods
          sh 'kubectl get pods -o wide'
        }
      }
    }
  }
}

