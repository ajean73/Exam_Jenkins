pipeline {
    environment {
        DOCKER_ID = "alba3838"
        DOCKER_TAG = "v.${BUILD_ID}.0"
        KUBECONFIG = credentials("config")
    }

agent any

stages {
        stage(' Docker Build Movie Service'){ // docker build image stage
            steps {
                script {
                sh '''
                docker rm -f jenkins
                "docker build -t $DOCKER_ID/movie-service-1:$DOCKER_TAG ./movie-service"
                sleep 6
                '''
                }
            }
        }
        stage('Docker run'){ // run container from our builded image
                steps {
                    script {
                    sh '''
                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/movie-service-1:$DOCKER_TAG
                    sleep 10
                    '''
                    }
                }
            }

        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
            steps {
                    script {
                    sh '''
                    curl localhost
                    '''
                    }
            }

        }
        stage('Docker Push'){ //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            }

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/movie-service-1:$DOCKER_TAG
                '''
                }
            }

        }


        stage(' Docker Build Cast Service'){ // docker build image stage
            steps {
                script {
                sh '''
                docker rm -f jenkins
                "docker build -t $DOCKER_ID/cast-service-1:$DOCKER_TAG ./cast-service"
                sleep 6
                '''
                }
            }
        }
        stage('Docker run'){ // run container from our builded image
                steps {
                    script {
                    sh '''
                    docker run -d -p 80:80 --name jenkins $DOCKER_ID/cast-service-1:$DOCKER_TAG
                    sleep 10
                    '''
                    }
                }
            }

        stage('Test Acceptance'){ // we launch the curl command to validate that the container responds to the request
            steps {
                    script {
                    sh '''
                    curl localhost
                    '''
                    }
            }

        }
        stage('Docker Push'){ //we pass the built image to our docker hub account
            environment
            {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") // we retrieve  docker password from secret text called docker_hub_pass saved on jenkins
            }

            steps {

                script {
                sh '''
                docker login -u $DOCKER_ID -p $DOCKER_PASS
                docker push $DOCKER_ID/cast-service-1:$DOCKER_TAG
                '''
                }
            }

        }


        stage('Deploy Movie Service in Dev') {
            environment {
                KUBECONFIG = credentials("config")
            }
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
            environment {
                KUBECONFIG = credentials("config")
            }
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
            environment {
                KUBECONFIG = credentials("config")
            }            
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
