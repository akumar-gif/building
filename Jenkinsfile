pipeline {
    agent any

    environment {
        REGISTRY_URL = "docker.io/akshay783/openshit-test"         // e.g., docker.io/yourusername
        IMAGE_NAME = "openshit-test"
        IMAGE_TAG = "latest"
        OPENSHIFT_PROJECT = "hello-world-project"
    }

    stages {
        stage('Clone Repository') {
            steps {
                // Replace <repository-url> with your source code repository URL
                git url: 'https://github.com/akumar-gif/building.git'
            }
        }

        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             // Build the Docker image using Docker CLI
        //             sh "docker build -t ${REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG} ."
        //         }
        //     }
        // }

        // stage('Push Docker Image') {
        //     steps {
        //         script {
        //             // Push the Docker image to your registry
        //             sh "docker push ${REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG}"
        //         }
        //     }
        // }

        stage('Deploy to OpenShift') {
            steps {
                script {
                    // Log in to OpenShift
                    withCredentials([string(credentialsId: 'openshift-token', variable: 'TOKEN')]) {
                        sh """
                            oc login <openshift-cluster-url> --token=$TOKEN
                            oc project ${OPENSHIFT_PROJECT}
                            
                            # Create or update the deployment
                            oc apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-world
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-world
        image: ${REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG}
        ports:
        - containerPort: 8080
EOF

                            # Expose the service
                            oc expose deployment/hello-world --port=8080 || true
                            oc expose svc/hello-world
                        """
                    }
                }
            }
        }
    }
}
