#!groovy

// variables
def gitBranch;
def codebuildProject = "acme-ci-nodejs-carbon-generic"
def artifactBucket = "acme-ci-jenkins-codebuild";
def artifactPath = "artifacts"
def artifactName = "acme-web-app"

node {
    stage('Dependencies') {
        checkout scm
        gitBranch = env.BRANCH_NAME;
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
            artifactNameOverride: artifactName
    }

    stage('Copy Artifacts') {
        sh "aws configure set s3.signature_version s3v4"
        sh "aws s3 cp s3://${artifactBucket}/${artifactPath}/${artifactName} ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
   }

   stage('Test Results') {
        junit "test-results/*.xml"
   }
}
