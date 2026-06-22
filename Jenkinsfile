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
                withCredentials([usernamePassword(
                    credentialsId: 'github-creds',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_PASS'
                )]) {
                    sh '''
                    rm -rf deploy-repo

                    git clone https://$GIT_USER:$GIT_PASS@github.com/sarforajs/deploy-repo.git
                    cd deploy-repo

                    sed -i "s/tag:.*/tag: $TAG/" deploy-repo/dev/mychart/values.yaml

                    git config user.name "jenkins"
                    git config user.email "jenkins@example.com"

                    git add .
                    git commit -m "update image tag to $TAG" || echo "No changes to commit"

                    git push https://$GIT_USER:$GIT_PASS@github.com/sarforajs/deploy-repo.git
                    '''
                }
            }
        }
    }
}
