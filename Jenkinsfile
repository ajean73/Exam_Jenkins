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

        stage('Deploy Movie Service in Dev') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        cp helm/my-app/values/values-movie.yml values.yml
                        sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                        helm upgrade --install movie-service helm/my-app --values=values.yml --namespace dev
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
                        cp helm/my-app/values/values-movie.yml values.yml
                        sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                        helm upgrade --install movie-service helm/my-app --values=values.yml --namespace qa
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
                        cp helm/my-app/values/values-movie.yml values.yml
                        sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                        helm upgrade --install movie-service helm/my-app --values=values.yml --namespace staging
                    '''
                }
            }
        }

        stage('Deploy Movie Service in Prod') {
            steps {
                script {
                    sh '''
                        rm -Rf .kube
                        mkdir .kube
                        cat $KUBECONFIG > .kube/config
                        cp helm/my-app/values/values-movie.yml values.yml
                        sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
                    '''
                }
            }
            input(message: 'Do you want to deploy in production ?', ok: 'Yes')
            steps {
                script {
                    sh '''
                        helm upgrade --install movie-service helm/my-app --values=values.yml --namespace prod
                    '''
                }
            }
        }

    }
}
