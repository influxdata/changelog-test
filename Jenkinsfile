pipeline {
  agent any

  stages {
    stage('Update changelog') {
      when {
        beforeAgent true
        anyOf {
          branch 'master'
          expression { BRANCH_NAME ==~ /^\d+(.\d+)*$/ }
        }
      }

      steps {
        sh "Updating CHANGELOG.md"
      }
    }
  }
}
