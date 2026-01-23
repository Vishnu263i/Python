pipeline
     agent any
     parameters {

       choice(
         name: 'OPERATION',
         choices: ['add','sub','mul'],
         description: 'Select Operation'
         )
         string(name: 'A',defaultvalue: '0',description: 'First number')
         string(name: 'B',defaultvalue: '0',description: 'second number')
     }
     stages {
       stage('calculate'){
          steps {
            script {
                def a=params.A as Integer
                def b=params.B as Interger
                def result=0
               if (params.OPERATION == 'add')
                 {
                   result=a+b
                 }  
               else if (params.OPERATION == 'sub')
                 {
                    result=a-b
                 }
               else if (params.OPERATION == 'mul')
                 {
                    result=a*b
                 }
                  
                 echo "Operation : ${params.OPERATION}"

                 echo "A: ${a}"
                 echo "B: ${b}"
                 echo "Result: ${result}"
                 }
               }
             }
          }
        }

              
