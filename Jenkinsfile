pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "akash0708/college-portal"
        PATH = "C:\\Program Files\\Docker\\Docker\\resources\\bin;${env.PATH}"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Akash89-eng/24MIS0132_ASS8_COLLEGE.git'
            }
        }

        stage('Check Docker') {
            steps {
                bat 'where docker'
                bat 'docker --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:latest .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-token',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {
                    bat 'docker login -u %USER% -p %PASS%'
                    bat 'docker push %DOCKER_IMAGE%:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kuberconfig',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat '''
                    set KUBECONFIG=%KUBECONFIG%

                    kubectl apply -f deployment.yaml --validate=false

                    kubectl rollout status deployment/college-portal
                    '''
                }
            }
        }

        stage('Verify Kubernetes') {
            steps {
                bat 'kubectl get deployment'
                bat 'kubectl get pods'
                bat 'kubectl get service'
            }
        }
    }
}
