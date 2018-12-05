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
        sh "aws s3 cp s3://acme-ci-jenkins-codebuild/artifacts/ ./artifacts.zip"
        sh "jar xf artifacts.zip && rm -f artifacts.zip"
        junit "test-results/*.xml"
   }
}
