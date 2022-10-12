#!/usr/bin/env groovy

def label = "tf-cicd-${UUID.randomUUID().toString()}"
def home = "/home/jenkins"
def workspace = "${home}/workspace/build-jenkins-operator"
def workdir = "${workspace}/src/github.com/jenkinsci/kubernetes-operator/"

podTemplate(label: label,
            serviceAccount: jenkins-operator-demo,
        containers: [
                containerTemplate(name: 'terraform', image: 'amaankhan4u/ubuntu:latest', ttyEnabled: true, command: 'sleep', args: '99d'),
        ],
        ) {

    node(label) {
        stage('Generate terraform plan') {
            container('terraform') {
                sh '''
                aws s3 ls
                '''
            }
        }
    }
}

