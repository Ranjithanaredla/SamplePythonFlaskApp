
pipeline {
agent any

```
environment {
    IMAGE_NAME = 'ranjitha28/devdoc'
    IMAGE_TAG = 'latest'
}

stages {
    stage('Check Tools') {
        steps {
            bat 'docker --version'
            bat 'kubectl get nodes'
        }
    }

    stage('Build Docker Image') {
        steps {
            echo 'Building Week 7 Flask application'
            bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
        }
    }

    stage('Login and Push to Docker Hub') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_TOKEN'
            )]) {
                bat '''
                    @echo off
                    echo Logging in to Docker Hub...
                    echo %DOCKER_TOKEN%| docker login -u "%DOCKER_USER%" --password-stdin
                    if errorlevel 1 exit /b 1

                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    if errorlevel 1 exit /b 1

                    docker logout
                '''
            }
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            bat 'kubectl apply -f deployment.yaml'
            bat 'kubectl apply -f service.yaml'
        }
    }

    stage('Verify Deployment') {
        steps {
            bat 'kubectl rollout status deployment/week7-flask-deployment --timeout=120s'
            bat 'kubectl get deployments'
            bat 'kubectl get pods'
            bat 'kubectl get services'
        }
    }
}

post {
    success {
        echo 'Week 9 pipeline completed successfully!'
    }

    failure {
        echo 'Pipeline failed. Check the Jenkins Console Output.'
    }
}
```

}
