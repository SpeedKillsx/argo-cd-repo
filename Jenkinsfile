pipeline {
    agent { label 'Jenkins-Agent' }

    environment {
        APP_NAME = 'registration-app-aws'
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Test SSH connection to GitHub") {
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
            steps {
                git branch: 'main', credentialsId: 'github-ssh', url: 'git@github.com:SpeedKillsx/argo-cd-repo.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "=== Before update ==="
                    cat registration-app-deployment.yaml

                    sed -i 's|${APP_NAME}.*|${APP_NAME}:${IMAGE_TAG}|g' registration-app-deployment.yaml

                    echo "=== After update ==="
                    cat registration-app-deployment.yaml
                """
            }
        }

        stage("Push to GitHub via SSH") {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        eval \$(ssh-agent -s)
                        ssh-add \$SSH_KEY
                        ssh-keyscan github.com >> ~/.ssh/known_hosts

                        git config --global user.name "SpeedKillsx"
                        git config --global user.email "amayaslabchri88@gmail.com"

                        git add registration-app-deployment.yaml
                        git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
                        git push git@github.com:SpeedKillsx/argo-cd-repo.git main
                    """
                }
            }
        }
    }
}
