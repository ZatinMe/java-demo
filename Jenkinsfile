pipeline {
    agent any

    environment {
        DOCKER_CREDS = 'docker_creds'
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker_creds')
        DOCKER_IMAGE_NAME = 'merazza/java:amd-'
        VERSION_FILE = 'version.txt'
    }

    stages {
        stage('Git checkout'){
            steps{
                git branch: 'main', credentialsId: 'jenkins_git', url: 'https://github.com/ZatinMe/java-demo.git'
            }
        }
        stage('Read Version') {
            steps {
                script {
                    def version = readFile("${VERSION_FILE}").trim()
                    def versionParts = version.split('\\.')
                    def major = versionParts[0].toInteger()
                    def minor = versionParts[1].toInteger()
                    def patch = versionParts[2].toInteger()
                    patch++
                    def newVersion = "${major}.${minor}.${patch}"
                    writeFile file: "${VERSION_FILE}", text: newVersion
                    env.BUILD_VERSION = newVersion
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    def jarFile = findFiles(glob: 'target/*.jar').files[0]
                    def dockerImageTag = "${DOCKER_IMAGE_NAME}:v${env.BUILD_VERSION}"
                    docker.build(dockerImageTag, "-f Dockerfile . --build-arg JAR_FILE=${jarFile}")
                }
            }
        }
        stage('Deploy Image') {
            steps {
                script {
                    // Use Jenkins credentials for Docker Hub login
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                        // Push the image
                        sh "docker push ${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}"
                    }
                }
            }
        }
    }
}
