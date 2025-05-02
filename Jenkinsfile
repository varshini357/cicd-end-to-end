pipeline {
    agent any 

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-creds', 
                    url: 'https://github.com/varshini357/cicd-end-to-end.git',
                    branch: 'main'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh '''
                        echo 'Build Docker Image'
                        docker build -t himavarshini123/cicd-e2e:latest .
                    '''
                }
            }
        }

        stage('Push the artifacts') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh '''
                            echo 'Logging into Docker Hub'
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            
                            echo 'Pushing Docker Image'
                            docker push himavarshini123/cicd-e2e:latest
                        '''
                    }
                }
            }
        }

        stage('Apply Kubernetes Manifest') {
            steps {
                script {
                    sh '''
                        echo 'Deploying to Kubernetes'
                        kubectl apply -f deploy/deploy.yaml
                    '''
                }
            }
        }
    }
}
