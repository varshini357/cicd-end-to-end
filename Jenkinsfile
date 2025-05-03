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
                        docker build -t himavarshini123/cicd-e2e:${BUILD_NUMBER} .
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
        
        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds', 
                        usernameVariable: 'GIT_USERNAME', 
                        passwordVariable: 'GIT_PASSWORD'
                    )]) {
                        sh '''
                            echo 'Updating deployment.yaml with new image tag'

                            sed -i "s|PLACEHOLDER_TAG|${BUILD_NUMBER}|" deploy/deploy.yaml
                            sed -i "s|PLACEHOLDER_TAG|${BUILD_NUMBER}|" deploy/pod.yaml
                            
                            cat deploy/deploy.yaml
                            cat deploy/pod.yaml
                            
                            git config user.name "varshini357" 
                            git config user.email "varshini8913@gmail.com"

                            git add deploy/deploy.yaml deploy/pod.yaml
                            git commit -m "Updated image tag to ${BUILD_NUMBER} | Jenkins Pipeline" || echo "No changes to commit"
                            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/varshini357/cicd-end-to-end.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
