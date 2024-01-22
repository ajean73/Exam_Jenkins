pipeline {
    environment {
        DOCKER_ID = "alba3838"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        KUBECONFIG = credentials("config")
    }

    agent any

    stages {
        stage('Build and Push Movie Service') {
            steps {
                script {
                    sh "docker build -t $DOCKER_ID/movie-service-1:$DOCKER_TAG ./movie-service"
                    sh "docker push $DOCKER_ID/movie-service-1:$DOCKER_TAG"
                }
            }
        }

        stage('Build and Push Cast Service') {
            steps {
                script {
                    sh "docker build -t $DOCKER_ID/cast-service-1:$DOCKER_TAG ./cast-service"
                    sh "docker push $DOCKER_ID/cast-service-1:$DOCKER_TAG"
                }
            }
        }

        stage('Deploy Movie Service in Dev') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie --values=./movie/values.yaml --set image.tag=${DOCKER_TAG} --namespace dev
                    '''
                }
            }
        }

        stage('Deploy Cast Service in Dev') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast --values=./cast/values.yaml --set image.tag=${DOCKER_TAG} --namespace dev
                    '''
                }
            }
        }

        stage('Deploy Movie Service in QA') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie --values=./movie/values.yaml --set image.tag=${DOCKER_TAG} --namespace qa
                    '''
                }
            }
        }

        stage('Deploy Cast Service in QA') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast --values=./cast/values.yaml --set image.tag=${DOCKER_TAG} --namespace qa
                    '''
                }
            }
        }

        stage('Deploy Movie Service in Staging') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie --values=./movie/values.yaml --set image.tag=${DOCKER_TAG} --namespace staging
                    '''
                }
            }
        }

        stage('Deploy Cast Service in Staging') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast --values=./cast/values.yaml --set image.tag=${DOCKER_TAG} --namespace staging
                    '''
                }
            }
        }

        stage('Deploy Movie Service in Prod') {
            steps {
                script {
                    // Create an Approval Button with a timeout of 15 minutes.
                    // This requires manual validation to deploy on the production environment
                    timeout(time: 15, unit: "MINUTES") {
                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
                    }

                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install movie-service ./movie --values=./movie/values.yaml --set image.tag=${DOCKER_TAG} --namespace prod
                    '''
                }
            }
        }

        stage('Deploy Cast Service in Prod') {
            steps {
                script {
                    // Create an Approval Button with a timeout of 15 minutes.
                    // This requires manual validation to deploy on the production environment
                    timeout(time: 15, unit: "MINUTES") {
                        input message: 'Do you want to deploy in production ?', ok: 'Yes'
                    }

                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        helm upgrade --install cast-service ./cast --values=./cast/values.yaml --set image.tag=${DOCKER_TAG} --namespace prod
                    '''
                }
            }
        }
    }
}
