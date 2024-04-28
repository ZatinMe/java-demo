pipeline {
    agent any
    def app

    environment {
        DOCKER_CREDS = 'docker_creds'
        DOCKER_REGISTRY_CREDENTIALS = credentials(DOCKER_CREDS)
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
                    app = docker.build(dockerImageTag, "-f Dockerfile . --build-arg JAR_FILE=${jarFile}")
                }
            }
        }
        stage('Push to Docker Registry') {
            docker.withRegistry('https://registry.hub.docker.com', DOCKER_REGISTRY_CREDENTIALS) {
                app.push("${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}")
            }
        }
    }
}
