pipeline {
    agent any

    environment {
        REGISTRY = "kumkammonteju"
        IMAGE_TAG = "${env.BUILD_NUMBER}"   // Version-wise tag
        KUBECONFIG_PATH = "${WORKSPACE}/kubeconfig"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'Gitcreds', url: 'https://github.com/Tejashwini-Ambati/Microservices.git', branch: 'main'
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {
                    def services = ["auth", "user"]
                    docker.withRegistry('https://index.docker.io/v1/', 'Dockercreds') {
                        services.each { service ->
                            sh """
                                docker build -t ${REGISTRY}/${service}:${IMAGE_TAG} ./services/${service}
                                docker push ${REGISTRY}/${service}:${IMAGE_TAG}
                            """
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                script {
                        def services = ["auth", "user"]

                    services.each { service ->

                        sh """
                            echo "Deploying ${service}"

                            # Update image in Kubernetes manifest
                            sed -i 's|image: ${REGISTRY}/${service}:.*|image: ${REGISTRY}/${service}:${IMAGE_TAG}|' k8smanifests/${service}-deployment.yaml

                            # Apply manifest
                            kubectl --kubeconfig="${KUBECONFIG}" apply -f k8smanifests/${service}-deployment.yaml

                            # Wait for rollout
                            kubectl --kubeconfig="${KUBECONFIG}" rollout status deployment/${service}-deployment
                        
                        """
                        }
                    }
                }
            }
        }
    }
}
