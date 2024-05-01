pipeline {
    agent any

    environment {
//         DOCKER_REGISTRY_CREDENTIALS = credentials('docker_creds')
        DOCKER_CREDENTIALS = credentials('docker_creds')
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
                sh 'mvn clean install'
            }
        }
        stage('Dockerize') {
            steps {
                script {
                    def files = findFiles(glob: 'target/*.jar')
                    if (files) {
                        def jarFile = files[0]
                        def dockerImageTag = "${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}"
                        docker.build(dockerImageTag, '.')
                    } else {
                        return
                    }
                }
            }
        }
        stage('Deploy Image') {
            steps {
                script {
                    docker.withRegistry('https://registry-1.docker.io/v2/', DOCKER_CREDENTIALS){
                        // Push the image
                        docker.image("${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}").push()
                    }
                }
            }
        }
    }
}
