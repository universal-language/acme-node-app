#!groovy

// variables
def gitBranch;

node {
    stage('Dependencies') {
        checkout scm
        gitBranch = env.BRANCH_NAME;
    }

    stage('Build & Test') {
        awsCodeBuild projectName: 'acme-ci-nodejs-carbon-generic', credentialsType: 'keys', region: 'us-east-1', sourceControlType: 'jenkins'
    }

    stage('Copy Artifacts') {
        sh "aws configure set s3.signature_version s3v4"
        sh "aws s3 cp s3://acme-ci-jenkins-codebuild/artifacts/generic/acme-ci-nodejs-carbon-generic ./artifacts.zip"
        sh "jar xf artifacts.zip && rm -f artifacts.zip"
        junit "test-results/*.xml"
   }
}
