pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'merazza/java:amd-'
        VERSION_FILE = 'version.txt'
        MAVEN_HOME = '/opt/maven'
        PATH = "$MAVEN_HOME/bin:$PATH"
        dockerImage = ''
        registryCredential = 'docker_creds'
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
//         stage('Build') {
//             steps {
//                 sh 'mvn clean install'
//             }
//         }
//         stage('Dockerize') {
//             steps {
//                 script {
//                     def files = findFiles(glob: 'target/*.jar')
//                     if (files) {
//                         def jarFile = files[0]
//                         def dockerImageTag = "${DOCKER_IMAGE_NAME}${env.BUILD_VERSION}"
//                         dockerImage = docker.build(dockerImageTag, '.')
//                     } else {
//                         return
//                     }
//                 }
//             }
//         }
//
//         stage('push Image') {
//             steps {
//                 script {
//                     docker.withRegistry( '', registryCredential ) {
//                         dockerImage.push()
//                     }
//                 }
//             }
//         }
        stage('Git Update') {
            steps {
                script {
                     withCredentials([usernamePassword(credentialsId: 'jenkins_git', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh "git config --global user.name '${GIT_USERNAME}'"
                        sh "git config --global user.email meraz.zatin@gmail.com"
                        sh 'git remote set-url origin https://${GIT_PASSWORD}@github.com/ZatinMe/java-demo.git'
                        sh "git add ${VERSION_FILE}"
                        sh "git commit -m 'Update version file'"
                        sh "git push origin main"  // Assuming you're pushing to the master branch
                    }
                }
            }
        }
    }
}
