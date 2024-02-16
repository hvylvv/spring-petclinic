pipeline {
    agent any
    tools {
            maven "MAVEN-3"
            dockerTool "docker"
        }

    stages {
        stage('Checkstyle') {
            steps {
                // Use Maven for Checkstyle
                sh 'mvn checkstyle:checkstyle'
                archiveArtifacts artifacts: 'target/checkstyle-result.xml', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                // Run tests with Maven
                sh 'mvn test'
            }
        }

        stage('Build') {
            steps {
                // Build without tests with Maven
                sh 'mvn package -DskipTests'
            }
        }

        stage('Create Docker Image') {
            steps {
                // Assuming Docker is installed and configured
                script {
                    def dockerImageTag = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()

                    // Build and tag the Docker image
                    sh "docker build -t spring-petclinic:${dockerImageTag} ."

                    // Push the Docker image to "mr" repository
                    sh "docker tag spring-petclinic:${dockerImageTag} mr/spring-petclinic:${dockerImageTag}"
                    withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'hvylvvv', passwordVariable: 'Latoya.1')]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    }
                    sh "docker push mr/spring-petclinic:${dockerImageTag}"
                }
            }
        }
    }
}
