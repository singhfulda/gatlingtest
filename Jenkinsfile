#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    stage('check java') {
        sh "java -version"
    }

    stage('prepare gradle') {
        sh "chmod +x ./gradlew"
    }

    stage('clean') {
        sh "./gradlew clean"
        sh "./gradlew npm_prune"
    }


    stage('npm install') {
        sh "./gradlew npmInstall"
    }

    stage('build war'){

        sh "./gradlew bootRepackage -Pprod -Preleaseversion=${projectVersion}"
    }

    stage('archiveArtifacts'){
        sh "mkdir -p build/docker"
        sh "cp src/main/docker/Dockerfile build/docker"
        sh "cp build/libs/*.war build/docker"

        archiveArtifacts artifacts: 'build/docker/**'
    }

    stage('build docker'){
        build job: 'build-docker-image', parameters: [string(name: 'imagename', value: imageName ), string(name: 'parentJobName', value: "${JOB_NAME}"), string(name: 'projectVersion', value: "${projectVersion}"),string(name: 'technicalBuildVersion', value: "${technicalBuildVersion}"), booleanParam(name: 'isLatest', value: params.isSnapshot )], propagate: true, wait: true
    }

    stage('e2e tests') {
        ansiColor('xterm') {
            sh "bash src/main/e2e/run-e2e-tests.sh"
        }
    }

    stage('start app') {
        ansiColor('xterm') {
            sh "./gradlew bootRun"
        }
    }

    stage('gatling load tests') {
        try {
            sh "./gradlew gatlingRun"
        } catch(err) {
            throw err
        }
    }

}
