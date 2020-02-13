#!/usr/bin/env groovy

pipeline {
  agent none
  environment {
    APP             = 'celery-prometheus-exporter'
    DOCKER_REGISTRY = '565166388327.dkr.ecr.us-east-1.amazonaws.com'
  }
  stages {
    stage('Build Container Image') {
      agent {
        label "kaniko-builder"
      }
      when {
        anyOf {
          branch 'master';
          tag pattern: "[0-9]+/[0-9]", comparator: "REGEXP"
        }
      }
      steps {
        // Alpine-based image used by the executor does not have `sh`
        // installed. Jenkinsfile sh directives need to invoke busybox sh
        // instead.
        container(name: 'kaniko', shell: '/busybox/sh') {
          withEnv(['PATH+EXTRA=/busybox:/kaniko']) {
            sh """#!/busybox/sh
            /kaniko/executor --context `pwd` --cache --dockerfile Dockerfile-celery4 --cache-repo ${env.DOCKER_REGISTRY}/lolatravel/layer-cache --destination ${env.DOCKER_REGISTRY}/lolatravel/${APP}:${env.GIT_COMMIT.take(7)}"""
          }
        }
      }
    }
  }
}
