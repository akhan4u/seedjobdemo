#!/usr/bin/env groovy

def label1 = "tf-cicd"

podTemplate(
            label: label1,
            name: 'akhan-demo',
            containers: [
                containerTemplate(name: 'terraform', image: 'amaankhan4u/ubuntu:latest', command: 'sleep', args: '99d'),
        ]
) 
        
{
    node(label) {
        stage('Generate terraform plan') {
            container('terraform') {
                sh 'aws s3 ls'
            }
        }
    }
}
