pipeline {
    agent { label "Jenkins-Agent" }
pipeline {
    agent { label "Jenkins-Agent" }
    environment {
              APP_NAME = "registration-app-aws"
    }

    stages {
        stage("Cleanup Workspace") {

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/speedKillsx/argo-cd-repo'
               }
        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/speedKillsx/argo-cd-repo'
               }
        }

        stage("Update the Deployment Tags") {
        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat registration-app-deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' registration-app-deployment.yaml
                   cat registration-app-deployment.yaml
                """
            }
                sh """
                   cat registration-app-deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' registration-app-deployment.yaml
                   cat registration-app-deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "speedKillsx"
                   git config --global user.email "amayaslabchri88@gmail.com"
                   git add registration-app-deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                  sh "git push https://github.com/speedKillsx/argo-cd-repo main"
                }
            }
        }
      
            steps {
                sh """
                   git config --global user.name "speedKillsx"
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
