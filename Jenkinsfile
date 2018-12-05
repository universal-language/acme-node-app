#!groovy

// variables
def gitBranch;
def codebuildProject = "acme-ci-nodejs-carbon-generic"
def artifactsBucket = "acme-ci-jenkins-codebuild";
def artifactsPath = "artifacts"

node {
    stage('Dependencies') {
        checkout scm
        gitBranch = env.BRANCH_NAME;
    }

    stage('Build & Test') {
        awsCodeBuild projectName: codebuildProject
            credentialsType: 'keys',
            region: 'us-east-1',
            sourceControlType: 'jenkins',
            artifactLocationOverride: artifactsBucket,
            artifactPackagingOverride: 'ZIP',
            artifactPathOverride: artifactsPath,
            artifactTypeOverride: 'S3',
            artifactNameOverride: 'acme-web-app'
    }

    stage('Copy Artifacts') {
        sh "aws configure set s3.signature_version s3v4"
        sh "aws s3 cp s3://acme-ci-jenkins-codebuild/artifacts/acme-web-app ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
        junit "test-results/*.xml"
   }
}
