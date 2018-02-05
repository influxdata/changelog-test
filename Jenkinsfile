pipeline {
  agent any

  stages {
    stage('Update changelog') {
      agent {
        docker {
          image      'jsternberg/changelog'
          alwaysPull true
        }
      }

      when {
        anyOf {
          branch 'master'
          expression { BRANCH_NAME ==~ /^\d+(.\d+)*$/ }
        }
      }

      steps {
        withCredentials(
          [[$class: "UsernamePasswordMultiBinding",
            credentialsId: "hercules-username-password",
            usernameVariable: "GITHUB_USER",
            passwordVariable: "GITHUB_TOKEN"]]) {
          sh "git changelog"
        }

        sshagent(credentials: ['jenkins-hercules-ssh']) {
          sh """
          if ! git diff --quiet; then
            git -c user.name='Hercules Mango Churchill' -c user.email='hercules@influxdata.com' commit -am 'Update changelog'
            git push origin HEAD:${BRANCH_NAME}
          fi
          """
        }
      }
    }
  }
}
