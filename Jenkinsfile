pipeline {
    agent any
    environment {
        APP_NAME = "my-node-app"
        DOCKER_REPO = "prasannakumar520/my-node-app"
    }
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Tag of the Docker image to deploy')
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
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Prasannakumar-Dev/devops-main main"
                }
            }
        }
    }
}
