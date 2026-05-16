pipeline {
    agent any
    environment {
        REGISTRY = "docker.io/sekharchadarasi"
        IMAGE = "nginx-sekhar"
        DOCKER_CREDENTIALS = "0a59ac1a-c31a-41e0-869c-25dcae7c0878"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $REGISTRY/$IMAGE:latest .'
            }
        }
        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }
        stage('Push') {
            steps {
                sh 'docker push $REGISTRY/$IMAGE:latest'
            }
        }
        stage('Update Deployment') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh '''
                    git config --global user.email "jenkins@example.com"
                    git config --global user.name "Jenkins"
                    git checkout main
                    sed -i "s|image:.*|image: $REGISTRY/$IMAGE:latest|" k8s/deployment.yaml
                    git add k8s/deployment.yaml
                    git commit -m "Update image to latest" || echo "No changes to commit"
                    git push https://$GIT_USER:$GIT_PASS@github.com/chchsekhar2000-prog/sekhar.git main
                    '''
                }
            }
        }
    }
}
