pipeline {
    agent any
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"  // Define IMAGE_TAG as an environment variable
        KUBECONFIG = '/home/masterserver/.kube/config'  // Set the path for kubectl config
    }
    stages {
        stage('stage01 - clean the working directory') {
            steps {
                cleanWs() 
            }
        }
        stage('stage02 - clone the repository') {
            steps {
                git branch: 'main', url: 'https://github.com/amitkumar0441/simple-jenkins-docker-kubernetesproject.git'
            }
        }
        stage('stage03 - create docker image') {
            steps {
                sh 'docker build -t amitkumar0441/dockerwebapp:${IMAGE_TAG} .'
            }
        }
        stage('stage 04 - push image to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockercredentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push amitkumar0441/dockerwebapp:${IMAGE_TAG}
                        docker rmi amitkumar0441/dockerwebapp:${IMAGE_TAG}
                        docker logout
                    '''
                }
            }
        }
        stage('stage05- modify the pod.yaml file') {
            steps {
                sh """
                    sed -i "s|\\\${IMAGE_TAG}|${IMAGE_TAG}|g" pod.yaml
                """
            }
        }
        stage('stage06-Run the kubernetes deployment') {
            steps {
                sh 'kubectl apply -f pod.yaml'
            }
        }
    }
}
