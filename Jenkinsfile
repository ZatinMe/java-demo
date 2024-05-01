pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'merazza/java:amd-'
        VERSION_FILE = 'version.txt'
        MAVEN_HOME = '/opt/maven'
        PATH = "$MAVEN_HOME/bin:$PATH"h
        DOCKER_HUB_USERNAME = credentials('docker_username')
        DOCKER_HUB_PASSWORD = credentials('docker_pass')
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
                        sh "docker login -u '${DOCKER_HUB_USERNAME}' -p '${DOCKER_HUB_PASSWORD}'"
                        sh "docker push '${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}'"
                }
            }
        }
    }
}
