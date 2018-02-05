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
        script {
          def commitId = currentBuild.getPreviousBuild().getChangeSets().getCommitId();
          withDockerContainer(image: "jsternberg/changelog") {
            withCredentials(
              [[$class: "UsernamePasswordMultiBinding",
                credentialsId: "hercules-username-password",
                usernameVariable: "GITHUB_USER",
                passwordVariable: "GITHUB_TOKEN"]]) {
              sh "git changelog ${commitId}"
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
