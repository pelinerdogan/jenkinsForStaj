pipeline {
    agent any

    environment {
        GIT_PROJECT_URL = 'https://github.com/pelinerdogan/stajproject.git'
        CONTAINER_NAME = 'flask-app'
        LATEST_TAG = 'latest'
        BRANCH_NAME = 'main'
        DOCKER_PROJECT_URL = 'https://github.com/pelinerdogan/stajproject.git'
        DOCKER_BRANCH_NAME = 'main'
        DOCKERFILE_NAME = 'Dockerfile'
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        DOCKER_REGISTRY_URL = '192.168.184.128:5000'
    }

    stages {
        stage('Get Dockerfile repository') {
            steps {
                script {
                    dir('mytmp') {
                        git branch: "${DOCKER_BRANCH_NAME}", url: "${DOCKER_PROJECT_URL}"
                    }
                    sh("cp mytmp/${DOCKERFILE_NAME} .")
                    deleteDir()
                }
            }
        }

        stage('Clone repository') {
            steps {
                git branch: "${BRANCH_NAME}", url: "${GIT_PROJECT_URL}"
            }
        }

        stage('Build & Register Docker Image') {
            steps {
                script {
                    sh 'docker --version' // Ensure Docker is installed
                    sh """
                        docker build -t ${CONTAINER_NAME}:${LATEST_TAG} .
                        docker tag ${CONTAINER_NAME}:${LATEST_TAG} ${DOCKER_REGISTRY_URL}/${CONTAINER_NAME}:${LATEST_TAG}
                        docker push ${DOCKER_REGISTRY_URL}/${CONTAINER_NAME}:${LATEST_TAG}
                    """
                }
            }
        }

        stage('Deploy') {
            when {
                expression { return params.DEPLOY }
            }
            steps {
                echo 'Deploying the project using Docker Compose...'
                sh 'docker-compose up -d'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            deleteDir()
        }
    }

    options {
        timeout(time: 1, unit: 'HOURS')
    }

    parameters {
        booleanParam(
            name: 'DEPLOY',
            defaultValue: false,
            description: 'Deploy the project using Docker Compose'
        )
    }
}
