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
    stage("Update Image Tag in infra:dev") {
      agent {
        label "kustomize"
      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        container('kustomize') {
          git branch: "master", credentialsId: 'github-ci-bot', url: 'https://github.com/lolatravel/prometheus-charts.git'
          sh """git config --global user.name 'lola-continous-integration' && git config --global user.email 'lola-continuous-integration@lola.com'"""
          sh """cd kubernetes/celery-prometheus-exporter/overlays/dev && kustomize edit set image celery-prometheus-exporter=${env.DOCKER_REGISTRY}/lolatravel/celery-prometheus-exporter:${env.GIT_COMMIT.take(7)}"""
          sh """git commit -am 'promote docker image for celery-prometheus-exporter:dev' || echo 'nothing to commit'"""
          withCredentials([usernamePassword(credentialsId: 'github-ci-bot', usernameVariable: 'username', passwordVariable: 'password')]){
            sh("git pull https://$username:$password@github.com/lolatravel/prometheus-charts && git push https://$username:$password@github.com/lolatravel/prometheus-charts")
          }
        }
      }
    }
    stage("Update Image Tag in infra:smoke") {
      agent {
        label "kustomize"
      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        container('kustomize') {
          git branch: "master", credentialsId: 'github-ci-bot', url: 'https://github.com/lolatravel/prometheus-charts.git'
          sh """git config --global user.name 'lola-continous-integration' && git config --global user.email 'lola-continuous-integration@lola.com'"""
          sh """cd kubernetes/celery-prometheus-exporter/overlays/smoke && kustomize edit set image celery-prometheus-exporter=${env.DOCKER_REGISTRY}/lolatravel/celery-prometheus-exporter:${env.GIT_COMMIT.take(7)}"""
          sh """git commit -am 'promote docker image for celery-prometheus-exporter:smoke' || echo 'nothing to commit'"""
          withCredentials([usernamePassword(credentialsId: 'github-ci-bot', usernameVariable: 'username', passwordVariable: 'password')]){
            sh("git pull https://$username:$password@github.com/lolatravel/prometheus-charts && git push https://$username:$password@github.com/lolatravel/prometheus-charts")
          }
        }
      }
    }
    stage("Update Image Tag in infra:staging") {
      agent {
        label "kustomize"
      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        container('kustomize') {
          git branch: "master", credentialsId: 'github-ci-bot', url: 'https://github.com/lolatravel/prometheus-charts.git'
          sh """git config --global user.name 'lola-continous-integration' && git config --global user.email 'lola-continuous-integration@lola.com'"""
          sh """cd kubernetes/celery-prometheus-exporter/overlays/staging && kustomize edit set image celery-prometheus-exporter=${env.DOCKER_REGISTRY}/lolatravel/celery-prometheus-exporter:${env.GIT_COMMIT.take(7)}"""
          sh """git commit -am 'promote docker image for celery-prometheus-exporter:staging' || echo 'nothing to commit'"""
          withCredentials([usernamePassword(credentialsId: 'github-ci-bot', usernameVariable: 'username', passwordVariable: 'password')]){
            sh("git pull https://$username:$password@github.com/lolatravel/prometheus-charts && git push https://$username:$password@github.com/lolatravel/prometheus-charts")
          }
        }
      }
    }
    stage("Update Image Tag in infra:production") {
      agent {
        label "kustomize"
      }
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        container('kustomize') {
          git branch: "master", credentialsId: 'github-ci-bot', url: 'https://github.com/lolatravel/prometheus-charts.git'
          sh """git config --global user.name 'lola-continous-integration' && git config --global user.email 'lola-continuous-integration@lola.com'"""
          sh """cd kubernetes/celery-prometheus-exporter/overlays/production && kustomize edit set image celery-prometheus-exporter=${env.DOCKER_REGISTRY}/lolatravel/celery-prometheus-exporter:${env.GIT_COMMIT.take(7)}"""
          sh """git commit -am 'promote docker image for celery-prometheus-exporter:production' || echo 'nothing to commit'"""
          withCredentials([usernamePassword(credentialsId: 'github-ci-bot', usernameVariable: 'username', passwordVariable: 'password')]){
            sh("git pull https://$username:$password@github.com/lolatravel/prometheus-charts && git push https://$username:$password@github.com/lolatravel/prometheus-charts")
          }
        }
      }
    }
  }
}
