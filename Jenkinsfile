pipeline {
  agent none
  stages {
    stage('Build step') {
      agent {
        docker {
          image 'maven:3.9.6-eclipse-temurin-17-alpine'
        }

      }
      steps {
        echo "Building app: ${env.BUILD_ID}"
        script {
          sh "mvn compile"
        }

      }
    }

    stage('Unit tests step') {
      agent {
        docker {
          image 'maven:3.9.6-eclipse-temurin-17-alpine'
        }

      }
      steps {
        echo 'Executing unit tests...'
        sh 'mvn clean test'
      }
    }

    stage('Package step') {
      parallel {
        stage('Package step') {
          agent {
            docker {
              image 'maven:3.9.6-eclipse-temurin-17-alpine'
            }

          }
          steps {
            echo 'Packaging app...'
            sh '''GIT_SHORT_COMMIT=$(echo $GIT_COMMIT | cut -c 1-7)
mvn versions:set -DnewVersion="$GIT_SHORT_COMMIT"
mvn versions:commit'''
            sh 'mvn package -DskipTests'
            archiveArtifacts '**/target/*.jar'
          }
        }

        stage('Docker build & package') {
          steps {
            script {
              docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
                def commitHash = env.GIT_COMMIT.take(7)
                def dockerImage = docker.build("paulws85/sysfoo:${commitHash}", "./")
                dockerImage.push()
                dockerImage.push("latest")
                dockerImage.push("dev")
              }
            }

          }
        }

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