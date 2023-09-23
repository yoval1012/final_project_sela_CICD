pipeline {
    agent {
        kubernetes {
            label 'slave'
            yamlFile 'build-pod.yaml'
            defaultContainer 'ez-docker-helm-build'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build('yoval1012/finalproject', '-f Dockerfile .')
                    dockerImage.inside {
                        sh 'helm version'
                    }
                }
            }
        }
        stage('Run Pytest') {
            steps {
                script {
                    echo "Running tests..."
                    sh 'docker run yoval1012/finalproject:latest pytest my_test.py'
                    echo "Tests completed successfully!"
                }
            }
        }
        

        stage('Push to Docker Hub') {
            steps {
                script {
                    def dockerImage = docker.image('yoval1012/finalproject')
                    docker.withRegistry('https://registry.hub.docker.com', 'yuval_dockerhub') {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Build and Push Helm Chart') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'yuval_dockerhub') {
                        sh 'helm package helm-chart'
                        sh 'helm push chart-0.1.0.tgz oci://registry-1.docker.io/yoval1012' 
                    }    
                }
            }
        }       
    }
}
