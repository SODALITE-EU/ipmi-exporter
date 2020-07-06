pipeline {
  options { disableConcurrentBuilds() }
  agent { label 'docker-slave' }
  tools {
    go 'Go'
  }
  stages {
    stage ('Pull repo code from github') {
      steps {
        checkout scm
      }
    }
    stage ('Build IPMI Exporter') {
     steps {
       sh """#!/bin/bash
            go get "github.com/Sirupsen/logrus"
            go get "github.com/prometheus/client_golang/prometheus"
            go get "github.com/prometheus/client_golang/prometheus/promhttp"
            go build -o IPMI_exporter
          """
     }
    }
   }
   post {
    failure {
        slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
    fixed {
        slackSend (color: '#6d3be3', message: "FIXED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})") 
    }
  }
}
      
