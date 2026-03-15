pipeline {
    agent any
    tools {
        jdk "JDK17"
        maven "MAVEN3"
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_GRP_REPO: "vprofile-grp-repo"
        NEXUS_PROTOCOL: 'http'
        NEXUS_URL: "172.31.33.235:8081"
        NEXUS_REPOSITORY: "vprofile-release"
        NEXUS_GRP_REPO: "vprofile-grp-repo"
        NEXUS_CREDENTIAL_ID: "nexuslogin"
        ARTVERSION = "${env.BUILD_ID}"
    }

    stages{
        stage("BUILD") {
            steps{
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('UNIT TEST') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Test') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('Code analysis with checkStyle'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated analysis result.'
                }
            }
        }
    }
}