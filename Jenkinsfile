#!groovy​

properties([[$class: 'BuildDiscarderProperty', strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

    stages{
        stage('build'){
steps
            {
                
                bat 'C:/Users/bharg/Downloads/Maven/apache-maven-3.9.6/bin/mvn clean'
    }
}

stage('build docker image') {
    node {
        mvn "clean package docker:build -DskipTests"
    }
}

def branch_type = get_branch_type "${env.BRANCH_NAME}"
def branch_deployment_environment = get_branch_deployment_environment branch_type

if (branch_deployment_environment) {
    stage('deploy') {
        if (branch_deployment_environment == "prod") {
            timeout(time: 1, unit: 'DAYS') {
                input "Deploy to ${branch_deployment_environment} ?"
            }
        }
        node {
            sh "echo Deploying to ${branch_deployment_environment}"
            //TODO specify the deployment
        }
    }

    if (branch_deployment_environment != "prod") {
        stage('integration tests') {
            node {
                sh "echo Running integration tests in ${branch_deployment_environment}"
                //TODO do the actual tests
            }
        }
    }
}

if (branch_type == "dev") {
    stage('start release') {
        timeout(time: 1, unit: 'HOURS') {
            input "Do you want to start a release?"
        }
        node {
            sshagent(['f1ad0f5d-df0d-441a-bea0-fd2c34801427']) {
                mvn("jgitflow:release-start")
            }
        }
    }
}

if (branch_type == "release") {
    stage('finish release') {
        timeout(time: 1, unit: 'HOURS') {
            input "Is the release finished?"
        }
        node {
            sshagent(['f1ad0f5d-df0d-441a-bea0-fd2c34801427']) {
                mvn("jgitflow:release-finish -Dmaven.javadoc.skip=true -DnoDeploy=true")
            }
        }
    }
}

if (branch_type == "hotfix") {
    stage('finish hotfix') {
        timeout(time: 1, unit: 'HOURS') {
            input "Is the hotfix finished?"
        }
        node {
            sshagent(['f1ad0f5d-df0d-441a-bea0-fd2c34801427']) {
                mvn("jgitflow:hotfix-finish -Dmaven.javadoc.skip=true -DnoDeploy=true")
            }
        }
    }
}

}
    }
