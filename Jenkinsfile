
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
                            extensions: [[$class: 'CloneOption', depth: 1, shallow: true]]
                            
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
                            extensions: [[$class: 'CloneOption', depth: 1, shallow: true]]
                            
                    ])
                }
        }

        stage('switch to master branch') {
            steps {
                sh "git checkout master"
            }
        }

        stage('Update YAML Image') {
            steps {
                script {
                    
                    def newImage = "dine297/ping-poller-doc-img:${BUILD_NUMBER}.0.0"
                    def yamlFilePath = "ping-poller-deploy.yaml"


                    def yaml = readYaml(file: "${yamlFilePath}")
                    yaml.spec.template.spec.containers[0].image = "${newImage}"
                    writeYaml(file: "${yamlFilePath}", data: yaml, overwrite: true )
                    
                }
            }
        }
        stage('Push to manifest') {
                steps {

                    withCredentials([string(credentialsId: 'dinegittoken', variable: 'GITTOKEN')]) {

                        sh """
                            git config user.email 'dine297@gmail.com'
                            git config user.name 'Dinesh Murthy'
                            git add .
                            git commit -m 'updated the image id'                            
                            git push https://${GITTOKEN}@github.com/dine297/ping-poller-manifest.git

                        """
                    }
                        //sh "git push"


                }
            }

        // stage('Apply Manifest') {
        //         steps {

        //             sh "kubectl apply -f ping-poller-deploy.yaml"
        //             sh "kubectl apply -f ping-poller-svc.yaml"


        //         }
        //     }


    }
}