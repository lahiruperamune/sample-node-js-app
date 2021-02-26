pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'Github', url: 'https://github.com/lahiruperamune/sample-node-js-app.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build("lahiruperamune/sample-node-app")
                    app.inside {
//                        sh 'echo $(curl localhost:3000)'
                    }
                }
            }
        }
       
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        stage('DeployToProduction') {          
            steps {
                input 'Deploy to Production?'
                
                    script {
                        sh "docker pull lahiruperamune/sample-node-app:${env.BUILD_NUMBER}"
                        
                        try {
                            sh "docker stop sample-node-app"
                            sh "docker rm sample-node-app"
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        
                        sh "docker run --name sample-node-app -d -p 80:3000 lahiruperamune/sample-node-app:${env.BUILD_NUMBER}"
                        
                    }
                
            }
        }
    }
}
