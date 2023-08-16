
pipeline {
    agent any
    
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
                        def dockerImageTag = "ping-poller:${BUILD_NUMBER}.0.0"
                        
                        // Build the Docker image using the Dockerfile in the repository
                        sh "docker build -t ${dockerImageTag} ."
                    }
                }
            }

    }
}