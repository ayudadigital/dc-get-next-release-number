#!groovy

@Library('github.com/tpbtools/jenkins-pipeline-library@v4.0.0') _

// Initialize global config
cfg = jplConfig('dc-get-next-release-number', 'bash', '', [email: env.CI_NOTIFY_EMAIL_TARGETS])

/**
 * Build and publish docker images
 *
 * @param nextReleaseNumber String Release number to be used as tag
 */
def buildAndPublishDockerImage(nextReleaseNumber = "") {
    if (nextReleaseNumber == "") {
        nextReleaseNumber = sh (script: "kd get-next-release-number .", returnStdout: true).trim().substring(1)
    }
    docker.withRegistry("", 'docker-token') {
        def customImage = docker.build("${env.DOCKER_ORGANIZATION}/${cfg.projectName}:${nextReleaseNumber}", "--pull --no-cache .")
        customImage.push()
        if (nextReleaseNumber != "beta") {
            customImage.push('latest')
        }
    }
}

pipeline {
    agent { label 'docker' }

    stages {
        stage ('Initialize') {
            steps  {
                jplStart(cfg)
            }
        }
        stage ('Bash linter') {
            steps {
                sh 'devcontrol run-bash-linter'
            }
        }
        stage ('Build') {
            steps {
                buildAndPublishDockerImage("beta")
            }
        }
        stage ('Make release') {
            when { branch 'release/new' }
            steps {
                buildAndPublishDockerImage()
                jplMakeRelease(cfg, true)
            }
        }
    }

    post {
        always {
            jplPostBuild(cfg)
        }
        cleanup {
            deleteDir()
        }
    }

    options {
        timestamps()
        ansiColor('xterm')
        buildDiscarder(logRotator(artifactNumToKeepStr: '20',artifactDaysToKeepStr: '30'))
        disableConcurrentBuilds()
        timeout(time: 10, unit: 'MINUTES')
    }
}