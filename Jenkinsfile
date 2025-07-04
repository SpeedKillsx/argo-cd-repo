pipeline {
    agent { label 'Jenkins-Agent' }

    

    environment {
        APP_NAME = 'registration-app-aws'
    }

    stages {
        stage('Check IMAGE_TAG') {
            when {
                expression {
                    return !IMAGE_TAG?.trim()
                }
            }
            steps {
                echo "IMAGE_TAG is empty. Skipping pipeline execution."
                
                script {
                    sh "${IMAGE_TAG} is not defined. Stopping pipeline execution."
                    currentBuild.result = 'NOT_BUILT'
                    error("Stopping pipeline: IMAGE_TAG is not defined.")
                }
            }
        }

        stage("Cleanup Workspace") {
            when {
                expression { return IMAGE_TAG?.trim() }
            }
            steps {
                cleanWs()
            }
        }

        stage("Test SSH connection to GitHub") {
            when {
                expression { return IMAGE_TAG?.trim() }
            }
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        eval \$(ssh-agent -s)
                        ssh-add \$SSH_KEY
                        ssh-keyscan github.com >> ~/.ssh/known_hosts
                        ssh -T git@github.com || true
                    """
                }
            }
        }

        stage("Checkout from SCM") {
            when {
                expression { return IMAGE_TAG?.trim() }
            }
            steps {
                git branch: 'main', credentialsId: 'github-ssh', url: 'git@github.com:SpeedKillsx/argo-cd-repo.git'
            }
        }

        stage("Update the Deployment Tags") {
            when {
                expression { return IMAGE_TAG?.trim() }
            }
            steps {
                sh """
                   cat registration-app-deployment.yaml
                   sed -i 's|\\(image: speedskillsx/registration-app-aws:\\).*|\\1${IMAGE_TAG}|' registration-app-deployment.yaml
                   cat registration-app-deployment.yaml
                """
            }
        }

        stage("Push to GitHub via SSH") {
            when {
                expression { return IMAGE_TAG?.trim() }
            }
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        eval \$(ssh-agent -s)
                        ssh-add \$SSH_KEY
                        ssh-keyscan github.com >> ~/.ssh/known_hosts

                        git config --global user.name "SpeedKillsx"
                        git config --global user.email "amayaslabchri88@gmail.com"

                        git add .
                        git commit -m "Updated Deployment Manifest for tag : ${IMAGE_TAG}" || echo "No changes to commit"
                        git push git@github.com:SpeedKillsx/argo-cd-repo.git main
                    """
                }
            }
        }
    }
}
