pipeline{
    agent {label 'Jenkins-Agent' }
    environment {
        APP_NAME = 'registration-app-aws'
    }
    stages{
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout Code") {
            steps {
                git branch: 'main', url: 'https://github.com/speedKillsx/argo-cd-repo.git' , credentialsId: 'github'
            }
        }

        stage("Update Deplotment File") {
            steps {
                script {
                   sh """cat registration-app-deployment.yaml
                        sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' registration-app-deployment.yaml
                        cat registration-app-deployment.yaml
                   """
                }
            }
        }

        stage("Check Git Authentication") {
            steps {
                    withCredentials([usernamePassword(
                        credentialsId: 'github',
                        usernameVariable: 'GIT_USERNAME',
                        passwordVariable: 'GIT_PASSWORD'
                    )]) {
                        sh """
                            echo "Testing Git authentication..."
                            git ls-remote https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/speedKillsx/argo-cd-repo.git
                        """
                    }
                }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github',              // ID de vos identifiants Jenkins (GitHub PAT)
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_PASSWORD'
                )]) {
                    sh '''
                        git config --global user.name "SpeedsKillsx"
                        git config --global user.email "amayaslabchri88@gmail.com"
                        git add registration-app-deployment.yaml
                        git commit -m "Updated Deployment Manifest" || echo "Nothing to commit"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/speedKillsx/argo-cd-repo.git main
                    '''
                }
            }
        }


        
    }
}