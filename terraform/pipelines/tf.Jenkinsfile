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
            stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([
                            choice(
                                choices: ['development', 'staging'],
                                name: 'DEPLOY_STAGE',
                                description: 'Teikametrics Environment'
                            ),
                        parameters([
                            choice(
                                choices: ['plan', 'apply'],
                                name: 'TF_ACTION',
                                description: 'Terraform Command'
                            )
                        ])
                    ])
                }
            }
        }
        stage('List S3 Bucket') {
            container('terraform') {
                sh 'aws s3 ls'
            }

            stage('Get authentication information') {
                container('terraform') {
                    sh 'aws sts get-caller-identity'
                }
            }
        }
    }
}
