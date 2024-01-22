pipeline {
    environment {
        DOCKER_ID = "alba3838"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        KUBECONFIG = credentials("config")
        DOCKER_PASS = credentials("DOCKER_HUB_PASS")
    }

    agent any

    stages {
        stage('Docker Build Movie Service') { // Je construis l'image Docker du service Movie
            steps {
                script {
                    sh """
                    docker build -t $DOCKER_ID/movie-service-1:$DOCKER_TAG ./movie-service
                    sleep 6
                    """
                }
            }
        }

        stage('Docker run Movie Service') { // Je lance le conteneur Docker du service Movie
            steps {
                script {
                    sh """
                    docker rm -f jenkins
                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/movie-service-1:$DOCKER_TAG
                    sleep 10
                    """
                }
            }
        }

        stage('Test Acceptance Movie Service') {
            steps {
                script {
                    sh """
                    curl localhost
                    """
                }
            }
        }

        stage('Docker Push Movie Service') { // Je pousse l'image sur DockerHub
            steps {
                script {
                    sh """
                    docker login -u $DOCKER_ID -p $DOCKER_PASS
                    docker push $DOCKER_ID/movie-service-1:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Docker Build Cast Service') { // Je construis l'image Docker du service Cast
            steps {
                script {
                    sh """
                    docker build -t $DOCKER_ID/cast-service-1:$DOCKER_TAG ./cast-service
                    sleep 6
                    """
                }
            }
        }

        stage('Docker run Cast Service') { // Je lance le conteneur Docker du service Cast
            steps {
                script {
                    sh """
                    docker rm -f jenkins
                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/cast-service-1:$DOCKER_TAG
                    sleep 10
                    """
                }
            }
        }

        stage('Test Acceptance Cast Service') {
            steps {
                script {
                    sh """
                    curl localhost
                    """
                }
            }
        }

        stage('Docker Push Cast Service') { // Je pousse l'image sur DockerHub
            steps {
                script {
                    sh """
                    docker login -u $DOCKER_ID -p $DOCKER_PASS
                    docker push $DOCKER_ID/cast-service-1:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Deploy Movie Service in Dev') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie-helm --values=./movie-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace dev
                    """
                }
            }
        }

        stage('Deploy Cast Service in Dev') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast-helm --values=./cast-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace dev
                    """
                }
            }
        }

        stage('Deploy Movie Service in QA') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie-helm --values=./movie-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace qa
                    """
                }
            }
        }

        stage('Deploy Cast Service in QA') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast-helm --values=./cast-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace qa
                    """
                }
            }
        }

        stage('Deploy Movie Service in Staging') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie-helm --values=./movie-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace staging
                    """
                }
            }
        }

        stage('Deploy Cast Service in Staging') {
            environment {
                KUBECONFIG = credentials("config")
            }
            steps {
                script {
                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast-helm --values=./cast-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace staging
                    """
                }
            }
        }

        stage('Deploy Movie Service in Prod') {
            environment {
                KUBECONFIG = credentials("config")
            }

            when {
                branch 'masterr'
            }

            steps {
                script {
                    timeout(time: 15, unit: "MINUTES") {
                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
                    }

                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie-helm --values=./movie-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace prod
                    """
                }
            }
        }

        stage('Deploy Cast Service in Prod') {
            environment {
                KUBECONFIG = credentials("config")
            }

            when {
              expression {
                env.BRANCH_NAME == 'masterr'
                }
            }

            steps {
                script {
                    timeout(time: 15, unit: "MINUTES") {
                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
                    }

                    sh """
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast-helm --values=./cast-helm/values.yaml --set image.tag=${DOCKER_TAG} --namespace prod
                    """
                }
            }
        }
    }
}
