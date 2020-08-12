pipeline {
  agent any
  stages {
    stage('Docker login') {
      steps {
        sh 'echo "$DCREDS_PSW" | docker login -u "$DCREDS_USR" --password-stdin'
      }
    }

    stage('clone down') {
      agent any
      steps {
        stash(excludes: '.git', name: 'code')
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
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash(excludes: '.git', name: 'code2')
          }
        }

        stage('test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('push docker app') {
      when {
        branch 'master'
      }
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code2'
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }

    stage('test component') {
      steps {
        sh 'ci/component-test.sh'
      }
    }

  }
  environment {
    DCREDS = credentials('docker_login')
    docker_username = 'arkaria'
  }
}