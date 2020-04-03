pipeline {
  agent any
  stages {
    stage('clone down') {
      agent {
        label 'host'
      }
      steps {
        stash excludes: '.git', name: 'code'
      }
    }
    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
          options {
            skipDefaultCheckout true
          }
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh label: '', script: 'ls -la'
            deleteDir()
            sh 'ls -la'
          }
        }

        stage('test app') {
          options {
            skipDefaultCheckout true
          }
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

  }
  post {
    always {
      deleteDir() /* clean up our workspace */
    }
  }
}