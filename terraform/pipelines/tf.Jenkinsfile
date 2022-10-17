#!/usr/bin/env groovy

def label = 'tf-cicd'

podTemplate(
            label: "$label",
            name: 'teikametrics-demo',
            containers: [
                containerTemplate(name: 'terraform', image: 'atlassian/pipelines-awscli:latest', command: 'sleep', args: '99d'),
            ],
            serviceAccount: 'jenkins-operator-demo',
        )

{
    node(label) {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [[$class: 'CloneOption', noTags: false, reference: 'origin', shallow: false]], userRemoteConfigs: [[credentialsId: '78fbecd8-0194-4231-9451-127c4ce102da', url: 'git@github.com:teikametrics/tm-infra-shared.git']]])

                stage('list files') {
                    container('terraform') {
                        sh 'ls -la'
                        sh 'tree'
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
