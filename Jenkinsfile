pipeline {
    agent any

    environment {
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker_creds')
        DOCKER_IMAGE_NAME = 'merazza/java:amd-'
        VERSION_FILE = 'version.txt'
        MAVEN_HOME = '/opt/maven'
        PATH = "$MAVEN_HOME/bin:$PATH"
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
                sh 'echo path is : $PATH'
                sh 'mvn --version'
                sh 'mvn clean install'
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    def files = findFiles(glob: 'target/*.jar')
                    if (files) {
                        def jarFile = files[0]
                    else 
                        return
                    def dockerImageTag = "${DOCKER_IMAGE_NAME}:v${env.BUILD_VERSION}"
                    docker.build(dockerImageTag, "-f Dockerfile . --build-arg JAR_FILE=${jarFile}")
                }
            }
        }
        stage('Push to Docker Registry') {
            steps {
                script {
                    docker.withRegistry('https://hub.docker.com', DOCKER_REGISTRY_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE_NAME}:v${env.BUILD_VERSION}").push()
                    }
                }
            }
        }
    }
}
