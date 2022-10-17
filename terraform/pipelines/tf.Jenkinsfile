#!/usr/bin/env groovy

def label = 'tf-cicd'

podTemplate(
            label: "$label",
            name: 'teikametrics-demo',
            containers: [
                containerTemplate(name: 'terraform', image: 'docker.io/teikadev/run-terraform:latest', command: 'sleep', args: '99d'),
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

        stage('list files in repo') {
            container('terraform') {
                sh 'ls -la'
                sh 'env'
            }
        }
        stage('Pull ECR container') {
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
        stage('list files') {
            container('terraform') {
                sh 'echo $DEPLOY_STAGE $TF_ACTION'
                sh 'ls -la'
            }
        }
        stage('List s3 buckets') {
            container('terraform') {
                sh 'aws s3 ls'
            }
        }
        stage('Get authentication information') {
            container('terraform') {
                sh 'aws sts get-caller-identity'
            }
        }
    }
}
