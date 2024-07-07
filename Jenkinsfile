pipeline {
    agent any
    environment {
        APP_NAME = "my-node-app"
        DOCKER_REPO = "prasannakumar520/my-node-app"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/Prasannakumar-Dev/devops-main'
            }
        }
        stage("Get Latest Image Tag") {
            steps {
                script {
                    def image = sh(
                        script: "curl -s https://hub.docker.com/v2/repositories/${DOCKER_REPO}/tags/latest | grep '\"name\"' | cut -d '\"' -f 4",
                        returnStdout: true
                    ).trim()
                    env.IMAGE_TAG = image
                }
            }
        }
        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo "Original deployment.yaml:"
                    cat deployment.yaml
                    
                    sed -i 's|image: ${DOCKER_REPO}:.*|image: ${DOCKER_REPO}:${IMAGE_TAG}|g' deployment.yaml
                    
                    echo "Updated deployment.yaml:"
                    cat deployment.yaml
                """
            }
        }
        stage("Push the changed deployment file to GitHub") {
            steps {
                sh """
                    git config --global user.name "Prasanna"
                    git config --global user.email "prasannakumar.akrabhi@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest with latest image tag"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/Prasannakumar-Dev/devops-main main"
                }
            }
        }
    }
}
