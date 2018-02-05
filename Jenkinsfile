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
        withCredentials(
          [[$class: "UsernamePasswordMultiBinding",
            credentialsId: "hercules-username-password",
            usernameVariable: "GITHUB_USER",
            passwordVariable: "GITHUB_TOKEN"]]) {
          sh "git changelog"
        }
      }
    }
  }
}
