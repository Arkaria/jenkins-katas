pipeline {
  agent any
  stages {
    stage('Docker login') {
      environment {
        DCREDS = credentials('docker_login')
      }
      steps {
        sh 'echo "$DCREDS_PSW" | docker login -u "$DCREDS_USR" --password-stdin'
      }
    }

    stage('clone down') {
      agent {
        node {
          label 'host'
        }

      }
      steps {
        stash(excludes: '/.git/', name: 'code')
      }
    }

    stage('Say Hello') {
      parallel {
        stage('Parrallel Execution') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
          }
        }

      }
    }

    stage('push docker app') {
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }

  }
}