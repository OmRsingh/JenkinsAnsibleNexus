pipeline {
    agent any
    tools {
        jdk "JDK17"
        maven "MAVEN3"
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_REPOGRP_ID= "vprofile-grp-repo"
        NEXUS_PROTOCOL= "http"
        NEXUS_URL= "172.31.33.235:8081"
        NEXUS_REPOSITORY= "vprofile-release"
        NEXUS_CREDENTIAL_ID= "nexuslogin"
        ARTVERSION = "${env.BUILD_ID}"
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"
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

        stage('Sonar Analysis') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps {
               withSonarQubeEnv("${SONARSERVER}") {
                   sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }

              timeout(time: 10, unit: 'MINUTES'){
                waitForQualityGate abortPipeline: true
              }
            }
        }

    }
}