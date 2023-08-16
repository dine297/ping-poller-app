
pipeline {
    agent any

    environment {

        KUBECONFIG = "C:\\\\Users\\\\1106322\\\\.kube\\\\config"

    }
    
    stages {
        stage('Checkout') {
                steps {
                    // Checkout the code from the Git repository
                    checkout([$class: 'GitSCM',
                            branches: [[name: '*/master']], // Change 'master' to your desired branch
                            userRemoteConfigs: [[url: 'https://github.com/dine297/ping-poller-app.git']], // Replace with your repo URL
                            extensions: [
                                [$class: 'CloneOption', depth: 1], // Shallow clone (depth: 1)
                            ]
                    ])
                }
        }
        stage('Build Docker Image') {
                steps {
                    script {
                        def dockerImageTag = "dine297/ping-poller-doc-img:${BUILD_NUMBER}.0.0"
                        
                        // Build the Docker image using the Dockerfile in the repository
                        sh "docker build -t ${dockerImageTag} ."
                    }
                }
            }
        stage('Push Docker Image to Docker Hub') {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'dinedockerhub', passwordVariable: 'DOCKERHUBPASS', usernameVariable: 'DOCKERHUBUSER')]) {

                        script {
                            def dockerImageTag = "dine297/ping-poller-doc-img:${BUILD_NUMBER}.0.0"

                            sh "docker login -u ${DOCKERHUBUSER} -p ${DOCKERHUBPASS}"

                            sh "docker push ${dockerImageTag}"
                        }

                    }

                }
            }
        stage('Clone Kube Manifest') {
                steps {
                    // Checkout the code from the Git repository
                    checkout([$class: 'GitSCM',
                            branches: [[name: '*/master']], // Change 'master' to your desired branch
                            userRemoteConfigs: [[url: 'https://github.com/dine297/ping-poller-manifest.git']], // Replace with your repo URL
                            extensions: [
                                [$class: 'CloneOption', depth: 1], // Shallow clone (depth: 1)
                            ]
                    ])
                }
        }
        stage('Apply Manifest') {
                steps {

                    sh "kubectl apply -f ping-poller-deploy.yaml"
                    sh "kubectl apply -f ping-poller-svc.yaml"


                }
            }


    }
}