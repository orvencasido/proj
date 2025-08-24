pipeline {
    agent any 

    environment {
        DOCKER_IMAGE = "orvencasido/devops-project-1"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                        docker rm -f devops-project-1 || true
                        docker run -d -p 80:80 --name devops-project-1 ${DOCKER_IMAGE} 
                    """
                }
            }
        }
    }
}