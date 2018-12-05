#!groovy

final CODEBUILD_PROJECT = "acme-ci-nodejs-carbon-generic"
final ARTIFACT_BUCKET = "acme-ci-jenkins-codebuild";
final ARTIFACT_PATH = "artifacts"
final ARTIFACT_NAME = "acme-web-app"

node {
    stage('Dependencies') {
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
            ARTIFACT_PATHOverride: ARTIFACT_PATH,
            artifactTypeOverride: 'S3',
            ARTIFACT_NAMEOverride: "${ARTIFACT_NAME}-${env.BUILD_NUMBER}"
    }

    stage('Copy Artifacts') {
        sh "aws s3 cp s3://${ARTIFACT_BUCKET}/${ARTIFACT_PATH}/${ARTIFACT_NAME}-${env.BUILD_NUMBER} ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
   }

   stage('Test Results') {
        junit "test-results/*.xml"
   }
}
