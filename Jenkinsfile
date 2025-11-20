pipeline {
    agent any
    tools {
        maven 'Maven3'
    }

    environment {
     PATH = "C:\\Program Files\\Docker\\Docker\\resources\\bin;${env.PATH}"
           JAVA_HOME = 'C:\\Program Files\\Java\\jdk-17'  // Adjust to your actual JDK pat
        SONARQUBE_SERVER = 'SonarQubeServer'  // The name of the SonarQube server configured in Jenkins

        DOCKERHUB_CREDENTIALS_ID = 'Docker_Hub'
        DOCKERHUB_REPO = 'michabl/sep2_week5_f2025'
        DOCKER_IMAGE_TAG = 'latest'


    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/michabl116/Tehtava_20_11_2025.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
                    steps {
                        withSonarQubeEnv('SonarQubeServer') {
                            bat """
                                ${tool 'SonarScanner'}\\bin\\sonar-scanner ^
                                -Dsonar.projectKey=devops-demo ^
                                -Dsonar.sources=src ^
                                -Dsonar.projectName=DevOps-Demo ^
                                -Dsonar.java.binaries=target/classes
                            """
                        }
                    }
                }



        stage('Build Docker Image') {
                    steps {
                        script {
                            docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
                            // Or specify Dockerfile path explicitly if needed
                            // docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}", "-f ./Dockerfile .")
                        }
                    }
                }

                stage('Push Docker Image to Docker Hub') {
                    steps {
                        script {
                           withCredentials([usernamePassword(credentialsId: 'Docker_Hub', usernameVariable: 'michabl', passwordVariable: '1Peru20243#')]) {
                                           bat """
                                               echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                                               docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}
                                           """
                            }
                        }
                    }
                }
    }
}
