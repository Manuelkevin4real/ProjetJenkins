#!/usr/bin/env groovy
pipeline {
  agent any
  environment { 
        CC = 'clang'
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
    }
    stages {
        stage('Build') {
            environment { 
                DEBUG_FLAGS = '-g'
            }
            steps {
                sh 'make' 
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true 
            }
        }

        stage('Test') {
              steps {
                  /* `make check` returns non-zero on test failures,
                  * using `true` to allow the Pipeline to continue nonetheless
                  */
                  sh 'make check || true' 
                  junit '**/target/*.xml' 
              }
          }

        stage('Deploy') {
              when {
                expression {
                  currentBuild.result == null || currentBuild.result == 'SUCCESS' 
                }
              }
              steps {
                  sh 'make publish'
              }
          }
    }    
}
