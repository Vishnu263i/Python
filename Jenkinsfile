pipeline {
  agent any
 
  parameters {
    choice(
      name: 'OPERATION',
      choices: ['add', 'sub', 'mul'],
      description: 'Select operation'
    )
    string(name: 'A', defaultValue: '0', description: 'First number')
    string(name: 'B', defaultValue: '0', description: 'Second number')
  }
 
  stages {
    stage('Validate & Calculate') {
      steps {
        script {
          // Validate numeric inputs
          if (!params.A?.isInteger() || !params.B?.isInteger()) {
            error "Both A and B must be integers. Received: A='${params.A}', B='${params.B}'"
          }
 
          Integer a = params.A.toInteger()
          Integer b = params.B.toInteger()
          def result
 
          switch (params.OPERATION) {
            case 'add':
              result = a + b
              break
            case 'sub':
              result = a - b
              break
            case 'mul':
              result = a * b
              break
            default:
              error("Unknown OPERATION: ${params.OPERATION}")
          }
 
          echo "Selected operation: ${params.OPERATION}"
          echo "Inputs: A=${a}, B=${b}"
          echo "Result: ${result}"
 
          // Make result visible on the job list
          currentBuild.description = "op=${params.OPERATION}, A=${a}, B=${b}, result=${result}"
        }
      }
    }
  }
 
  post {
    success {
      echo 'Calculation completed successfully.'
    }
    failure {
      echo 'Pipeline failed. Check validation or operation value.'
    }
  }
}
