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

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "SpeedKillsx"
                   git config --global user.email "amayaslabchri88@gmail.com"
                   git add registration-app-deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                  sh "git push https://github.com/speedKillsx/argo-cd-repo main"
                }
            }
        }
    }
}