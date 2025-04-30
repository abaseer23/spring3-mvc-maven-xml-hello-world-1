pipeline {
    agent any 
    tools {
        // Make sure "maven" is correctly defined under Global Tool Configuration
        maven "maven"
    }
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "52.90.95.78:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "hello"
        // Jenkins credential ID for Nexus
        NEXUS_CREDENTIAL_ID = "nexus"
    }
    stages {
        stage("Clone Code") {
            steps {
                git 'https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }
        stage("Maven Build") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true install'
            }
        }
        stage("Publish to Nexus") {
            steps {
                script {
                    // Use readMavenPom instead of XmlParser
                    def pom = readMavenPom file: 'pom.xml'
                    def groupId = pom.groupId
                    def artifactId = pom.artifactId
                    def packaging = pom.packaging
                    
                    def filesByGlob = findFiles(glob: "target/*.${packaging}")
                    if (filesByGlob.length == 0) {
                        error "No artifact found matching 'target/*.${packaging}'"
                    }

                    def artifactPath = filesByGlob[0].path
                    def artifactExists = fileExists(artifactPath)

                    if (artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${groupId}, packaging: ${packaging}, version: ${BUILD_NUMBER}"
                        
                        nexusArtifactUploader(
                            nexusVersion: "${NEXUS_VERSION}",
                            protocol: "${NEXUS_PROTOCOL}",
                            nexusUrl: "${NEXUS_URL}",
                            groupId: groupId,
                            version: "${BUILD_NUMBER}",
                            repository: "${NEXUS_REPOSITORY}",
                            credentialsId: "${NEXUS_CREDENTIAL_ID}",
                            artifacts: [
                                [
                                    artifactId: artifactId,
                                    classifier: '',
                                    file: artifactPath,
                                    type: packaging
                                ],
                                [
                                    artifactId: artifactId,
                                    classifier: '',
                                    file: "pom.xml",
                                    type: "pom"
                                ]
                            ]
                        )
                    } else {
                        error "*** File not found: ${artifactPath}"
                    }
                }
            }
        }
    }
}
