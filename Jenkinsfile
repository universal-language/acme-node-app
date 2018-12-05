#!groovy

final codebuildProject = "acme-ci-nodejs-carbon-generic"
final artifactBucket = "acme-ci-jenkins-codebuild";
final artifactPath = "artifacts"
final artifactName = "acme-web-app"

node {
    stage('Dependencies') {
        checkout scm
        sh "env"
    }

    stage('Build & Test') {
        awsCodeBuild projectName: codebuildProject,
            credentialsType: 'keys',
            region: 'us-east-1',
            sourceControlType: 'jenkins',
            artifactLocationOverride: artifactBucket,
            artifactPackagingOverride: 'ZIP',
            artifactPathOverride: artifactPath,
            artifactTypeOverride: 'S3',
            artifactNameOverride: "${artifactName}-${env.BUILD_NUMBER}"
    }

    stage('Copy Artifacts') {
        sh "aws configure set s3.signature_version s3v4"
        sh "aws s3 cp s3://${artifactBucket}/${artifactPath}/${artifactName}-${env.BUILD_NUMBER} ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
   }

   stage('Test Results') {
        junit "test-results/*.xml"
   }
}
