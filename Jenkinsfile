pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t vle7-myapp:latest .'
            }
        }

        stage('Load Image into Minikube') {
            steps {
                sh '''
                    docker save vle7-myapp:latest -o /tmp/vle7-myapp.tar
                    docker cp /tmp/vle7-myapp.tar minikube:/tmp/vle7-myapp.tar
                    docker exec minikube docker load -i /tmp/vle7-myapp.tar
                    rm -f /tmp/vle7-myapp.tar
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f deployment.yaml
                    kubectl rollout restart deployment/myapp
                    kubectl rollout status deployment/myapp --timeout=180s
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get deployments'
                sh 'kubectl get svc'
            }
        }
    }
}
