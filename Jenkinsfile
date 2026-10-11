pipeline {
agent any


environment {
    IMAGE_NAME = 'ranjitha28/devdoc'
    IMAGE_TAG = 'latest'
    KUBECONFIG = 'C:\\Users\\ranji\\.kube\\config'
}

stages {
    stage('Check Tools') {
        steps {
            echo 'Checking Docker and Kubernetes...'

            bat 'docker --version'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" get nodes'
        }
    }

    stage('Build Docker Image') {
        steps {
            echo 'Building the Week 7 Flask application...'

            bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
        }
    }

    stage('Login and Push to Docker Hub') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )
            ]) {
                bat '''
                    @echo off
                    echo Logging in to Docker Hub...

                    echo %DOCKER_TOKEN%| docker login -u "%DOCKER_USER%" --password-stdin
                    if errorlevel 1 exit /b 1

                    echo Pushing Docker image...
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    if errorlevel 1 exit /b 1

                    docker logout
                '''
            }
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            echo 'Applying Kubernetes Deployment...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" apply -f deployment.yaml'

            echo 'Applying Kubernetes Service...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" apply -f service.yaml'
        }
    }

    stage('Verify Deployment') {
        steps {
            echo 'Waiting for the deployment to become ready...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" rollout status deployment/week7-flask-deployment --timeout=120s'

            echo 'Checking Kubernetes deployments...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" get deployments'

            echo 'Checking Kubernetes pods...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" get pods'

            echo 'Checking Kubernetes services...'

            bat 'kubectl --kubeconfig="%KUBECONFIG%" get services'
        }
    }
}

post {
    success {
        echo 'Week 9 pipeline completed successfully!'
    }

    failure {
        echo 'Pipeline failed. Check the Console Output for the first error.'
    }
}

}
