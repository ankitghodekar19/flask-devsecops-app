pipeline {
agent any

```
tools {
    sonarQubeScanner 'sonar-scanner'
}

environment {
    IMAGE_NAME = "ankitghodekar/flask-devsecops-app"
    IMAGE_TAG = "latest"
}

stages {

    stage('Checkout') {
        steps {
            git branch: 'main',
            url: 'https://github.com/ankitghodekar19/flask-devsecops-app.git'
        }
    }

    stage('SonarQube Analysis') {
        steps {
            withSonarQubeEnv('sonar-server') {
                sh '''
                sonar-scanner \
                  -Dsonar.projectKey=flask-devsecops-app \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://18.116.239.98:9000
                '''
            }
        }
    }

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
        }
    }

    stage('Push Docker Image') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhubcred',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    stage('Deploy Container') {
        steps {
            sh '''
            docker stop flaskapp || true
            docker rm flaskapp || true

            docker run -d \
              --name flaskapp \
              -p 5000:5000 \
              $IMAGE_NAME:$IMAGE_TAG
            '''
        }
    }
}
```

}

