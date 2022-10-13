#!/usr/bin/env groovy

def label = "tf-cicd"

podTemplate(
            label: "$label",
            name: 'akhan-demo',
            containers: [
                containerTemplate(name: 'terraform', image: 'atlassian/pipelines-awscli:latest', command: 'sleep', args: '99d'),
            ],
            serviceAccount: 'jenkins-operator-demo',
        ) 
        
{
    node(label) {
        stage('Generate terraform plan') {
            container('terraform') {
                sh 's3 ls'
            }
        }
    }
}
