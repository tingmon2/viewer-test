pipeline {
  agent any
  environment {
    NEW_VERSION = '1.0.0'
  }
  parameters {
    booleanParam(name: 'testBool', defaultValue: true, description: '')
  }

  stages {

    stage("build") {
      when {
        expression {
          params.testBool
        }
      }
      steps {
        echo "building... version ${NEW_VERSION}"
      }
    }

        stage("test") {
      steps {
        echo 'testing...'
      }
    }

        stage("deploy") {
      steps {
        echo 'deploying...'
      }
    }

  }
}
