pipeline {
  agent any
  stages {
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

  }
}