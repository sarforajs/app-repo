pipeline {
    agent any

    environment {
        IMAGE_NAME = "kubesarforaj/devops-app"
        TAG = "${BUILD_NUMBER}"
        DEPLOY_REPO = "https://github.com/sarforajs/deploy-repo.git"
    }

    stages {

        stage('Checkout App Repo') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$TAG ./app'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$TAG'
            }
        }

        stage('Update Deploy Repo') {
            steps {
                sh '''
                git clone $DEPLOY_REPO
                cd deploy-repo

                sed -i "s/tag:.*/tag: $TAG/" mychart/values.yaml

                git add .
                git commit -m "update image tag to $TAG"
                git push
                '''
            }
        }
    }
}
