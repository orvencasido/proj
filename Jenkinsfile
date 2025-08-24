pipeline {
    agent any 

    parameters {
        booleanParam(name: 'CLEAN_CACHE', defaultValue: false, description: 'Clean Docker build cache before building')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Tag for the Docker image')
        choice(name: 'DEPLOY_ENV', choices: ['dev', 'staging', 'prod'], description: 'Choose the environment to deploy')
    }

    environment {
        DOCKER_IMAGE = "orvencasido/devops-project-1:${params.IMAGE_TAG}"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    def buildCmd = "docker build -t ${DOCKER_IMAGE} ."
                    if (params.CLEAN_CACHE) {
                        buildCmd = "docker build --no-cache -t ${DOCKER_IMAGE} ."
                    }
                    sh buildCmd
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
                    echo "Deploying to environment: ${params.DEPLOY_ENV}"

                    sh """
                        docker rm -f devops-project-1 || true
                        docker run -d -p 80:80 --name devops-project-1 ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}
