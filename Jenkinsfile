pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
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
                        docker build -t himavarshini123/cicd-e2e:${BUILD_NUMBER}
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
                            docker push himavarshini123/cicd-e2e:${BUILD_NUMBER}
                        '''
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM') {
            steps {
                git credentialsId: 'github-creds', 
                    url: 'https://github.com/varshini357/cicd-end-to-end.git',
                    branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds', 
                        usernameVariable: 'GIT_USERNAME', 
                        passwordVariable: 'GIT_PASSWORD'
                    )]) {
                        sh '''
                            cat deploy/deploy.yaml
                            sed -i "s/32/${BUILD_NUMBER}/g" deploy/deploy.yaml
                            cat deploy/deploy.yaml
                            
                            git config user.name "varshini357" 
                            git config user.email "varshini8913@gmail.com"

                            git add deploy/deploy.yaml
                            git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/varshini357/cicd-end-to-end.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
