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
                    def pom = readMavenPom file: "pom.xml"
                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}")

                    if (filesByGlob.length == 0) {
                        error "No artifact found matching 'target/*.${pom.packaging}'"
                    }

                    def artifactPath = filesByGlob[0].path
                    def artifactExists = fileExists(artifactPath)

                    if (artifactExists) {
                        echo "*** Uploading: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version: ${BUILD_NUMBER}"

                        nexusArtifactUploader(
                            nexusVersion: "${NEXUS_VERSION}",
                            protocol: "${NEXUS_PROTOCOL}",
                            nexusUrl: "${NEXUS_URL}",
                            groupId: pom.groupId,
                            version: "${BUILD_NUMBER}",
                            repository: "${NEXUS_REPOSITORY}",
                            credentialsId: "${NEXUS_CREDENTIAL_ID}",
                            artifacts: [
                                [
                                    artifactId: pom.artifactId,
                                    classifier: '',
                                    file: artifactPath,
                                    type: pom.packaging
                                ],
                                [
                                    artifactId: pom.artifactId,
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

            }
        }
    }
}
