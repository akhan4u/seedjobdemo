#!/usr/bin/env groovy
//comment

def label = 'tf-cicd'

podTemplate(
            label: "$label",
            name: "tf-cicd-$BUILD_NUMBER",
            containers: [
                containerTemplate(name: 'terraform', image: 'docker.io/teikadev/run-terraform:v1', command: 'sleep', args: '99d', alwaysPullImage: true),
            ],
            serviceAccount: 'jenkins-operator-demo',
        )

{
    node(label) {
        checkout([
        $class: 'GitSCM', branches: [[name: '*/main']],
        extensions: [[$class: 'CloneOption', noTags: false, reference: 'origin', shallow: false],[$class: 'PathRestriction', excludedRegions: '', includedRegions: 'terraform-db-dump-instance/*']],
        userRemoteConfigs: [[credentialsId: '21831901-560a-4450-ab61-a24f0fcd0b54', url: 'git@github.com:teikametrics/akhan-testing.git']]]
        )

        stage('List Files In Application Repo') {
            container('terraform') {
                sh 'echo "The Workspace for the job is $WORKSPACE"'
            }
        }
        stage('Generate Terraform Plan') {
            container('terraform') {
            sh '''
                ls -la
                cd terraform-db-dump-instance/
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
