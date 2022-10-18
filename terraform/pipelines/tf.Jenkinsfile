#!/usr/bin/env groovy

def label = 'tf-cicd'

podTemplate(
            label: "$label",
            name: 'teikametrics-demo',
            containers: [
                containerTemplate(name: 'terraform', image: 'docker.io/teikadev/run-terraform:v1', command: 'sleep', args: '99d'),
            ],
            serviceAccount: 'jenkins-operator-demo',
        )

{
    node(label) {
        checkout([
        $class: 'GitSCM', branches: [[name: '*/master']],
        extensions: [[$class: 'CloneOption', noTags: false, reference: 'origin', shallow: false]],
        userRemoteConfigs: [[credentialsId: '78fbecd8-0194-4231-9451-127c4ce102da', url: 'git@github.com:teikametrics/tm-infra-shared.git']]]
        )

        stage('List Files In Repo') {
            container('terraform') {
                sh 'ls -la'
                sh 'env'
            }
        }
        stage('Pull ECR Container') {
            container('terraform') {
            sh '''
                ENV="$DEPLOY_STAGE"
                TF_CMD="$TF_ACTION"
                GIT_REMOTE_ORIGIN_URL="$(git config --get remote.origin.url)"
                GIT_REPO="$(echo "$GIT_REMOTE_ORIGIN_URL" | sed s:.*/:: | sed s/\\.git//)"
                GIT_REPO_PATH="$(git rev-parse --show-prefix)"
                TF_STATE_PATH="$GIT_REPO/$GIT_REPO_PATH"
                echo "$ENV $TF_CMD $GIT_REMOTE_ORIGIN_URL $GIT_REPO $GIT_REPO_PATH $TF_STATE_PATH"
            '''
            }
        }
        stage('Generate Terraform Plan') {
            container('terraform') {
            sh '''
                ENV="$DEPLOY_STAGE"
                TF_CMD="$TF_ACTION"
                GIT_REMOTE_ORIGIN_URL="$(git config --get remote.origin.url)"
                GIT_REPO="$(echo "$GIT_REMOTE_ORIGIN_URL" | sed s:.*/:: | sed s/\\.git//)"
                GIT_REPO_PATH="$(git rev-parse --show-prefix)"
                TF_STATE_PATH="$GIT_REPO/$GIT_REPO_PATH"
                cd aws/environment-opensearch/
                tf-wrapper
            '''
            }
        }
        stage('Get Authentication Information') {
            container('terraform') {
                sh 'aws sts get-caller-identity'
            }
        }
    }
}
