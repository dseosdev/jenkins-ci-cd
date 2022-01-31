pipeline {
    /*agent {
      label 'kubepods'
    }*/
    agent any
    environment {
        DOCKER_IMAGE_NAME = "dseos/jenkins-ci-cd"
        
    }
    stages {    
        stage('Build Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'main'
            }
            steps {
                milestone(1)
                script {
                    git branch: 'main',
                    credentialsId: 'github_key',
                    url: 'git@github.com:dseosdev/jenkins-ci-cd.git'

                    sh "ls -lat"
                    kubernetesDeploy(
                        configs: "php-deployment.yaml", 
                        kubeconfigId: "aks-pruebas",
                        enableConfigSubstitution: true
                        )
                }
            }
        }
    }
}