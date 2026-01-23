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
        stage('Calculate') {
            steps {
                script {
                    def a = params.A as Integer
                    def b = params.B as Integer
                    def result
 
                    switch (params.OPERATION) {
                        case 'add': result = a + b; break
                        case 'sub': result = a - b; break
                        case 'mul': result = a * b; break
                        default: error("Unknown OPERATION: ${params.OPERATION}")
                	    }
			}
		}
	}
	}
}
