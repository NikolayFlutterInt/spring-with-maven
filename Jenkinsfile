pipeline {

    agent {
        label 'my-agent-1'
    }

    tools {
        nodejs 'Maven 3.9.2'
        dockerTool 'docker'
    }
    triggers {
        githubPush()
    }

    environment {
      DOCKERHUB_CREDENTIALS = credentials('DockerHub')
      IMAGE_NAME = 'ndimovflutter/mynodejsapp'
    }

    stages {
        stage('Clean') {
            steps {
                cleanWs()
            }
        }
        stage('CloneRepo') {
            steps {
                git branch: 'main', url: 'https://github.com/NikolayFlutterInt/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn ci'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('DockerLogin') {
            steps {
                sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
            }
        }
        stage('DockerBuildAndTag') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('DockerPush') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker container rm -f mynodejsapp || true'
                sh 'docker container run -d --name mynodejsapp ndimovflutter/mynodejsapp'
            }
        }
    }
 }
