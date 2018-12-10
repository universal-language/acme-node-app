#!groovy

final CODEBUILD_PROJECT = "acme-ci-nodejs-carbon-generic"
final ARTIFACT_BUCKET = "acme-ci-jenkins-codebuild";
final ARTIFACT_PATH = "artifacts"
final ARTIFACT_NAME = "acme-web-app"

node {
    stage('Dependencies') {
        sh "env"
        checkout scm
        sh "aws configure set s3.signature_version s3v4"
    }

    stage('Build & Test') {
        awsCodeBuild projectName: CODEBUILD_PROJECT,
            credentialsType: 'keys',
            region: 'us-east-1',
            sourceControlType: 'jenkins',
            artifactLocationOverride: ARTIFACT_BUCKET,
            artifactPackagingOverride: 'ZIP',
            artifactPathOverride: ARTIFACT_PATH,
            artifactTypeOverride: 'S3',
            artifactNameOverride: "${ARTIFACT_NAME}-${env.BUILD_NUMBER}"
    }

    stage('Copy Artifacts') {
        sh "aws s3 cp s3://${ARTIFACT_BUCKET}/${ARTIFACT_PATH}/${ARTIFACT_NAME}-${env.BUILD_NUMBER} ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
   }

   stage('Test Results') {
        junit "test-results/*.xml"
   }

    if ("master" == env.BRANCH_NAME) {
        stage('Deploy Stage') {
            echo "Deploying stage"
        }
    }
}

if ("master" == env.BRANCH_NAME) {
    stage('Production') {
        timeout(time: 1, unit: 'DAYS') {
            input message: "Do you want to deploy to PROD?"
        }
        node {
            stage('Deploy Production') {
                echo "Deploying prod"
            }
        }
    }
}
