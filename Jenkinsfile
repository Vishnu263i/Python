
// Jenkinsfile - Choice-Parameterized Calculator
pipeline {
  agent any

  parameters {
    choice(
      name: 'OPERATION',
      choices: [
        'add',
        'subtract',
        'multiply',
        'divide',
        'power',
        'modulo'
      ],
      description: 'Choose the math operation to perform'
    )
    string(
      name: 'A',
      defaultValue: '0',
      description: 'Left operand (supports integers and decimals)'
    )
    string(
      name: 'B',
      defaultValue: '0',
      description: 'Right operand (supports integers and decimals)'
    )
    booleanParam(
      name: 'ROUND_RESULT',
      defaultValue: false,
      description: 'Round the result? (applies after calculation)'
    )
    string(
      name: 'ROUND_SCALE',
      defaultValue: '2',
      description: 'Number of decimal places when rounding (e.g., 0..10)'
    )
    choice(
      name: 'ROUND_MODE',
      choices: [
        'HALF_UP', 'HALF_DOWN', 'HALF_EVEN',
        'UP', 'DOWN', 'CEILING', 'FLOOR', 'UNNECESSARY'
      ],
      description: 'Rounding mode if rounding is enabled'
    )
  }

  options {
    timestamps()
    ansiColor('xterm')
    disableConcurrentBuilds()
  }

  stages {
    stage('Show Inputs') {
      steps {
        echo "OPERATION  : ${params.OPERATION}"
        echo "A          : ${params.A}"
        echo "B          : ${params.B}"
        echo "ROUND      : ${params.ROUND_RESULT} (scale=${params.ROUND_SCALE}, mode=${params.ROUND_MODE})"
      }
    }

    stage('Validate Parameters') {
      steps {
        script {
          // Validate numeric inputs
          def isDecimal = { s ->
            try {
              new BigDecimal(s as String)
              return true
            } catch (Throwable ignored) {
              return false
            }
          }

          if (!isDecimal(params.A)) {
            error("Parameter A must be a valid number. Got: '${params.A}'")
          }
          if (!isDecimal(params.B)) {
            error("Parameter B must be a valid number. Got: '${params.B}'")
          }

          // Validate ROUND_SCALE if rounding on
          if (params.ROUND_RESULT) {
            if (!(params.ROUND_SCALE ==~ /^[0-9]{1,2}$/)) {
              error("ROUND_SCALE must be an integer between 0 and 99. Got: '${params.ROUND_SCALE}'")
            }
          }

          // Division / Modulo by zero checks
          def b = new BigDecimal(params.B)
          if (params.OPERATION == 'divide' && b.compareTo(BigDecimal.ZERO) == 0) {
            error("Division by zero is not allowed.")
          }
          if (params.OPERATION == 'modulo' && b.compareTo(BigDecimal.ZERO) == 0) {
            error("Modulo by zero is not allowed.")
          }
        }
      }
    }

    stage('Calculate') {
      steps {
        script {
          BigDecimal a = new BigDecimal(params.A)
          BigDecimal b = new BigDecimal(params.B)

          // Helper for power (BigDecimal.pow only supports integer exponents)
          def powDecimal = { BigDecimal base, BigDecimal exp ->
            if (exp.scale() == 0) {
              // Integer exponent → use native BigDecimal pow
              return base.pow(exp.intValueExact())
            } else {
              // Non-integer exponents: approximate via Math.pow using double
              // (Precision limited; acceptable for simple calculator use)
              double approx = Math.pow(base.doubleValue(), exp.doubleValue())
              return new BigDecimal(approx as String)
            }
          }

          BigDecimal result
          switch (params.OPERATION) {
            case 'add':
              result = a.add(b)
              break
            case 'subtract':
              result = a.subtract(b)
              break
            case 'multiply':
              result = a.multiply(b)
              break
            case 'divide':
              // Safe divide with context; default scale 16 and HALF_UP
              result = a.divide(b, 16, java.math.RoundingMode.HALF_UP)
              break
            case 'power':
              result = powDecimal(a, b)
              break
            case 'modulo':
              // Use remainder for BigDecimal
              result = a.remainder(b)
              break
            default:
              error("Unsupported operation: ${params.OPERATION}")
          }

          // Optional rounding
          if (params.ROUND_RESULT) {
            int scale = params.ROUND_SCALE as int
            def rm = java.math.RoundingMode.valueOf(params.ROUND_MODE)
            // For operations like power/divide producing many decimals:
            result = result.setScale(scale, rm)
          }

          // Persist and print
          env.CALC_RESULT = result.stripTrailingZeros().toPlainString()
          echo "Result: ${env.CALC_RESULT}"

          // Also write to a file artifact (optional)
          writeFile file: 'calculator_result.txt', text: "${env.CALC_RESULT}\n"
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'calculator_result.txt', onlyIfSuccessful: false
    }
    success {
      echo "✔ Calculation completed. Result = ${env.CALC_RESULT}"
    }
    failure {
      echo "✖ Calculation failed. Check validation and inputs."
    }
  }
}

