pipeline{
    agent any
    stages{
        stage('checkout') {
            steps {
                echo 'Checking out the code...'
                git branch: 'main', url: 'https://github.com/MohammedOsamaHassan/flask-kubernetes.git'
            }
        }
        stage('build') {
            steps {
                echo 'Building image'
                sh 'docker build -t mohamedosama45/flask-app:latest .'
            }
            post {
                success {
                    echo 'Build successful'
                }
                failure {
                    echo 'Build failed'
                }
            }
        }
        stage('push'){
            steps{
                echo 'Pushing image'
                withCredentials([usernamePassword(credentialsId: 'dockercre', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh ''' 
                    echo $PASSWORD | docker login -u $USERNAME --password-stdin
                    docker push mohamedosama45/flask-app:latest
                    '''
                }
            }
            post {
                success {
                    echo 'Push successful'
                }
                failure {
                    echo 'Push failed'
                }
            }        
        }
        stage('deploy') {
            steps {
                echo 'Deploying to server'
                sh '''
                export KUBECONFIG=/root/.kube/k3s.yaml 
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
            post {
                success {
                    echo 'Deployment successful'
                    sh '''
                    export KUBECONFIG=/root/.kube/k3s.yaml
                    kubectl get pods -o wide
                    kubectl get services
                    '''
                }
                failure {
                    echo 'Deployment failed'
                }
            }
        }

    }
    
}