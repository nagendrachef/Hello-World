pipeline {
    agent any

    parameters {
        string(name: 'NAMESPACE', defaultValue: 'default', description: 'Kubernetes Namespace')
        string(name: 'RELEASE_NAME', defaultValue: 'my-release', description: 'Helm Release Name')
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker Image Tag')
    }

    environment {
        KUBECONFIG = credentials('kubeconfig') // Jenkins credential ID for kubeconfig
        HELM_HOME = '/usr/local/bin/helm' // Adjust the path if necessary
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://your-repo-url.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def app = docker.build("your-docker-repo/your-app:${params.IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://your-docker-registry-url', 'docker-credentials-id') {
                        app.push("${params.IMAGE_TAG}")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    helm upgrade --install ${params.RELEASE_NAME} ./helm-chart-directory \
                        --namespace ${params.NAMESPACE} \
                        --set image.tag=${params.IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
