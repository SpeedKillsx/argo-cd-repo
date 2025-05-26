pipeline{
    agent {label 'Jenkins-Agent' }
    environment {
        APP_NAME = 'registration-app-aws'
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                    dir("argo-cd-repo") {
                        checkout scm
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/SpeedKillsx/argo-cd-repo'
                }
               }
               
        }

        stage("Update the Deployment Tags") {
            steps {
                dir("argo-cd-repo") {
                    sh """
                    cat registration-app-deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' registration-app-deployment.yaml
                    cat registration-app-deployment.yaml
                    """
                }
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                dir("argo-cd-repo") {
                    sh """
                    git config --global user.name "SpeedKillsx"
                    git config --global user.email "amayaslabchri88@gmail.com"
                    git add registration-app-deployment.yaml
                    git commit -m "Updated Deployment Manifest"  || echo "No changes to commit"
                    git status
                    """
                    
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/SpeedKillsx/argo-cd-repo main"
                    }
                }
            }
        }
      
    }
}
