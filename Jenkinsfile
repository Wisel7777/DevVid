pipeline {
    agent any
    tools {
        maven "Maven_Home"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "41.83.93.55:8081"
        NEXUS_REPOSITORY = "java-app"
        NEXUS_CREDENTIAL_ID = "NEXUS_CRED"
    }
    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    git branch: 'master', credentialsId: 'githubwithpassword', url: 'https://github.com/Wisel7777/DevVid.git';
                }
            }
        }
        stage("Maven Build") {
            steps {
                script {
                    sh "mvn package -DskipTests=true"
                }
            }
        }
        stage("SonarQube Scanner") {
            agent any
            steps {
              withSonarQubeEnv('Sonarqube_server Scanner') {
                sh 'mvn clean package sonar:sonar'
              }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: 'pom.org.example',
                            version: 'pom.1.0-SNAPSHOT',
                            repository: 'Maventest',
                            credentialsId: 'NEXUS_CRED',
                            artifacts: [
                                [artifactId: 'pom.Tes',
                                classifier: '',
                                file: 'artifactPath',
                                type: 'pom.packaging'],
                                [artifactId:' pom.Tes',
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}
