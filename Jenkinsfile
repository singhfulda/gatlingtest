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
    }

//    stage('npm install') {
//        sh "./gradlew npmInstall"
//    }

    stage('build war'){

        sh "./gradlew bootRepackage -Pprod"
    }

    stage('archiveArtifacts'){
        sh "mkdir -p build/docker"
        sh "cp src/main/docker/Dockerfile build/docker"
        sh "cp build/libs/*.war build/docker"

        archiveArtifacts artifacts: 'build/docker/**'
    }

//    stage('build docker'){
//        build job: 'build-docker-image', parameters: [string(name: 'imagename', value: "myimage" ), string(name: 'parentJobName', value: "myjob"), string(name: 'projectVersion', value: "myproject"),string(name: 'technicalBuildVersion', value: "first"), booleanParam(name: 'isLatest', value: true )], propagate: true, wait: true
//    }

//    stage('e2e tests') {
//
//            sh "bash src/main/e2e/run-e2e-tests.sh"
//
//    }

    stage('start app') {

                sh "./gradlew bootRun"

            sh "./gradlew gatlingRun"



    }

}
