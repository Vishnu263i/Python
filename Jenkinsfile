pipeline {
  agent none   // we'll specify agents per stage

  stages {
    stage('Build on Linux') {
      agent { label 'linux' }   // make sure you have a node with this label
      steps {
        sh 'echo "Building on Linux..."'
        sh 'uname -a'
      }
    }

    stage('Test on Windows') {
      agent { label 'windows' } // make sure you have a Windows node with this label
      steps {
        bat 'echo Testing on Windows...'
        bat 'ver'
      }
    }
  }
}
