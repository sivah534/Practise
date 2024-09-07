pipeline {
    agent any

   /* environment {
        // Define any environment variables you need
        APP_NAME = 'my-web-app'
        DOCKER_IMAGE = 'my-docker-repo/my-web-app:latest'
    }
*/
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repository
                git branch: 'main', url: 'https://github.com/my-org/my-web-app.git'
            }
        }

        stage('Build') {
            steps {
                // Build the application
                script {
                    if (fileExists('pom.xml')) {
                        sh 'mvn clean install'
                    } else if (fileExists('build.gradle')) {
                        sh './gradlew build'
                    } else {
                        error 'No build file found'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                // Run unit tests
                script {
                    if (fileExists('pom.xml')) {
                        sh 'mvn test'
                    } else if (fileExists('build.gradle')) {
                        sh './gradlew test'
                    } else {
                        error 'No test file found'
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                // Build Docker image
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Push') {
            steps {
                // Push Docker image to the repository
                withCredentials([usernamePassword(credentialsId: 'docker-repo-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application
                script {
                    // Add your deployment logic here
                    // For example, you might use kubectl to deploy to Kubernetes
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }

    post {
        always {
            // Clean up actions that should run after the pipeline (successful or not)
            cleanWs()
        }

        success {
            // Notify success
            echo 'Pipeline completed successfully'
        }

        failure {
            // Notify failure
            echo 'Pipeline failed'
        }
    }
}
