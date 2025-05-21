pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "elmasibrahimaga/springboot-k8s:latest"
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/elmaas-ibrahimaga/MySpringBootApp.git'
            }
        }

        stage('Build JAR') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    export DOCKER_TLS_VERIFY=1
                    export DOCKER_HOST=tcp://192.168.49.2:2376
                    export DOCKER_CERT_PATH=/home/elmas/.minikube/certs
                    export MINIKUBE_ACTIVE_DOCKERD=minikube
                    docker build -t $DOCKER_IMAGE .
                '''
            }
        }


        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
