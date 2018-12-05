#!groovy

// variables
def gitBranch;

node {
    stage('Dependencies') {
        checkout scm
        gitBranch = env.BRANCH_NAME;
    }

    stage('Build & Test') {
        awsCodeBuild projectName: 'acme-ci-nodejs-carbon-generic',
            credentialsType: 'keys',
            region: 'us-east-1',
            sourceControlType: 'jenkins',
            artifactLocationOverride: 'acme-ci-jenkins-codebuild',
            artifactPathOverride: 'artifacts',
            artifactTypeOverride: 'S3',
            artifactNameOverride: 'acme-web-app'
    }

    stage('Copy Artifacts') {
        sh "aws configure set s3.signature_version s3v4"
        sh "aws s3 cp s3://acme-ci-jenkins-codebuild/artifacts-acme-web-app ./artifacts.zip"
        sh "unzip -o artifacts.zip && rm -f artifacts.zip"
        junit "test-results/*.xml"
   }
}
