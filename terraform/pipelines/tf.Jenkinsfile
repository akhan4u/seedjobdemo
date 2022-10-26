#!/usr/bin/env groovy

def label = 'tf-cicd'

podTemplate(
            label: "$label",
            name: "tf-cicd-$BUILD_NUMBER",
            containers: [
                containerTemplate(name: 'terraform', image: 'docker.io/teikadev/run-terraform:v1', command: 'sleep', args: '99d', alwaysPullImage: true),
            ],
            serviceAccount: 'jenkins-operator-demo',
        )

pipeline {
    triggers { pollSCM '* * * * *' }
    {
    node(label) {
        checkout([
        $class: 'GitSCM', branches: [[name: '*/master']],
        extensions: [[$class: 'CloneOption', noTags: false, reference: 'origin', shallow: false]],
        extensions: [[$class: 'PathRestriction', excludedRegions: '', includedRegions: 'terraform-db-dump-instance/*']],
        userRemoteConfigs: [[credentialsId: '78fbecd8-0194-4231-9451-127c4ce102da', url: 'git@github.com:teikametrics/akhan-testing.git']]]
        )

        stage('List Files In Application Repo') {
            container('terraform') {
                sh 'echo "The Workspace for the job is $WORKSPACE"'
            }
        }
        stage('Generate Terraform Plan') {
            container('terraform') {
            sh '''
                cd aws/environment-opensearch/
                export ENV="$DEPLOY_STAGE"
                export TF_CMD="$TF_ACTION"
                GIT_REMOTE_ORIGIN_URL="$(git config --get remote.origin.url)"
                GIT_REPO="$(echo "$GIT_REMOTE_ORIGIN_URL" | sed s:.*/:: | sed s/\\.git//)"
                GIT_REPO_PATH="$(git rev-parse --show-prefix)"
                export TF_STATE_PATH="$GIT_REPO/$GIT_REPO_PATH"
                tf-wrapper
            '''
            }
        }
    }
    }
}
