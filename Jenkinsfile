pipeline {
  agent any

  stages {
    stage('Update changelog') {
      when {
        anyOf {
          branch 'master'
          expression { BRANCH_NAME ==~ /^\d+(.\d+)*$/ }
        }
      }

      steps {
        sh "docker pull jsternberg/changelog"
        withDockerContainer(image: "jsternberg/changelog") {
          withCredentials(
            [[$class: "UsernamePasswordMultiBinding",
              credentialsId: "hercules-username-password",
              usernameVariable: "GITHUB_USER",
              passwordVariable: "GITHUB_TOKEN"]]) {
            script {
              if (env.GIT_PREVIOUS_SUCCESSFUL_COMMIT) {
                sh "git changelog ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}"
              } else {
                sh "git changelog"
              }
            }
          }
        }

        sshagent(credentials: ['jenkins-hercules-ssh']) {
          sh """
          set -e
          if ! git diff --quiet; then
            git -c user.name='Hercules Mango Churchill' -c user.email='hercules@influxdata.com' commit -am 'Update changelog'
            git -c remote.origin.pushurl=git@github.com:influxdata/changelog-test.git push origin HEAD:${BRANCH_NAME}
          fi
          """
        }
      }
    }
  }
}
