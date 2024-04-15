// Define the pipeline with any available agent
pipeline {
  agent any

  // Define tools used in the pipeline
  tools {
    maven 'maven_3_5_0'  // Maven tool with version 3.5.0
  }

  // Define stages in the pipeline
  stages {
    // Stage 1: Checkout code and build with Maven
    stage('Build Maven') {
      steps {
        // Checkout code from GitHub repository
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/main']],
          extensions: [],
          userRemoteConfigs: [[url: 'https://github.com/ShazShoaib/JenkinsPipelineMaven']]
        ])
        // Build the project using Maven (clean and install)
        sh 'mvn clean install'
      }
    }

    // Stage 2: Build a Docker image
    stage('Build Docker Image') {
      steps {
        script {
          // Build Docker image with tag and current directory context
          sh 'docker build -t shazshoaib/jenkins-pipeline .'
        }
      }
    }

    // Stage 3: Push the image to Docker Hub
    stage('Push image to Hub') {
      steps {
        script {
          // Retrieve password from credential and login to Docker Hub
          withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
            sh 'docker login -u shazshoaib -p ${dockerhubpwd}'
          }
          // Push the built image to Docker Hub
          sh 'docker push shazshoaib/jenkins-pipeline'
        }
      }
    }

    // Stage 4: Deploy the application to Kubernetes
    stage('Deploy to k8s') {
      steps {
        script {
          // Deploy using kubernetesDeploy function with config file and credentials
          kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
        }
      }
    }
  }
}
