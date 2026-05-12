pipeline {
  agent any
  stages {
    stage('Build step') {
      steps {
        echo "Building app: ${env.BUILD_ID}"
        script {
          sh "mvn compile"
        }

      }
    }

    stage('Unit tests step') {
      steps {
        echo 'Executing unit tests...'
        sh 'mvn clean test'
      }
    }

    stage('Package step') {
      steps {
        echo 'Packaging app...'
        sh '''GIT_SHORT_COMMIT=$(echo $GIT_COMMIT | cut -c 1-7)
mvn versions:set -DnewVersion="$GIT_SHORT_COMMIT"
mvn versions:commit'''
        sh 'mvn package -DskipTests'
        archiveArtifacts '**/target/*.jar'
      }
    }

  }
  tools {
    maven 'Maven 3.9.15'
  }
  post {
    always {
      echo 'Pipeline finished...'
    }

    success {
      echo 'Build Success!'
    }

    failure {
      echo 'Build Failed! Check BlueOcean logs for details.'
    }

    aborted {
      echo 'Build was cancelled.'
    }

  }
}