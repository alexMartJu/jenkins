pipeline {
     agent any

     tools {
          nodejs "Node"
     }

     parameters {
          string(name: 'EXECUTOR', defaultValue: '', description: 'Nombre de la persona que ejecuta la pipeline')
          string(name: 'MOTIVO', defaultValue: '', description: 'Motivo por el cual se ejecuta la pipeline')
          string(name: 'CHAT_ID', defaultValue: '', description: 'Chat ID de Telegram para las notificaciones')
     }

     stages {
          stage('Dependencias') {
               steps {
                    script {
                         echo "Instalando dependencias..."
                         bat 'npm install'
                         echo "Instalando CLI de Vercel..."
                         bat 'npm install -g vercel'
                         echo "Verificando la instalación de la CLI de Vercel..."
                         bat 'vercel --version'
                    }
               }
          }

          stage('Petición_de_datos') {
               steps {
                    script {
                         echo "Executor: ${params.EXECUTOR}"
                         echo "Motivo: ${params.MOTIVO}"
                         echo "Chat ID: ${params.CHAT_ID}"
                    }
               }
          }

          stage('Linter') {
               steps {
                    script {
                         echo "Ejecutando ESLINT"
                         def lintResult = bat script: 'npx eslint .', returnStatus: true

                         if (lintResult != 0) {
                              writeFile file: 'linter_result.txt', text: 'failure'
                              error "Se encontraron errores en el linter."
                         } else {
                              writeFile file: 'linter_result.txt', text: 'success'
                         }
                         echo "Linter ejecutado correctamente."
                    }
               }
          }

          stage('Test') {
               steps {
                    script {
                         echo "Ejecutando tests en Jest"
                         def testResult = bat(script: 'npm test', returnStatus: true)

                         if (testResult != 0) {
                              writeFile file: 'test_result.txt', text: 'failure'
                              error "Se encontraron errores en los tests."
                         } else {
                              writeFile file: 'test_result.txt', text: 'success'
                         }
                         echo "Todos los tests pasaron correctamente."
                    }
               }
          }

          stage('Build') {
               steps {
                    script {
                         echo "Realizando el build."
                         def buildResult = bat script: 'npm run build', returnStatus: true

                         if (buildResult != 0) {
                              error "El proceso de build falló."
                         }
                         echo "Build realizada correctamente."
                    }
               }
          }

          stage('Update_Readme') {
               steps {
                    script {
                         def testResult = readFile('test_result.txt').trim()

                         echo "Actualizando el README.md con el resultado de los tests (${testResult})..."

                         bat """
                         echo "Ejecutando el script updateReadme.js con TEST_RESULT=${testResult}..."
                         node ./jenkinsScripts/updateReadme.js ${testResult}
                         """

                         writeFile file: 'update_readme_result.txt', text: 'Correcto'
                    }
               }
          }

          stage('Vercel') {
               when {
                    expression {
                         currentBuild.result == null || currentBuild.result == 'SUCCESS'
                    }
               }
               steps {
                    script {
                         withCredentials([string(credentialsId: 'VERCEL_TOKEN', variable: 'VERCEL_TOKEN')]) {
                              echo "Iniciando el despliegue en Vercel"
                              def deployResult = bat(
                                   script: """
                                   call jenkinsScripts\\deployToVercel.bat %VERCEL_TOKEN%
                                   """,
                                   returnStatus: true
                              )
                              if (deployResult != 0) {
                                   writeFile file: 'deploy_to_vercel_result.txt', text: 'failure'
                                   error "El despliegue en Vercel falló."
                              } else {
                                   writeFile file: 'deploy_to_vercel_result.txt', text: 'success'
                              }
                         }
                    }
               }
          }

          stage('Notificación') {
               steps {
                    script {
                         withCredentials([string(credentialsId: 'TELEGRAM_TOKEN', variable: 'TELEGRAM_TOKEN')]) {
                              def linterResult = readFile('linter_result.txt').trim()
                              def testResult = readFile('test_result.txt').trim()
                              def deployToVercelResult = readFile('deploy_to_vercel_result.txt').trim()

                              def message = "Se ha ejecutado la pipeline de Jenkins con los siguientes resultados: " +
                                   "Linter_stage: ${linterResult}, " +
                                   "Test_stage: ${testResult}, " +
                                   "Deploy_to_Vercel_stage: ${deployToVercelResult}"

                              def envioTelegram = bat (
                                   script: """
                                   call jenkinsScripts\\sendTelegramMessage.bat %TELEGRAM_TOKEN% ${params.CHAT_ID} "${message}"
                                   """,
                                   returnStatus: true
                              )
                              if (envioTelegram != 0) {
                                   error "El envío de la notificación a Telegram falló. Revisa el log para más detalles."
                              } else {
                                   echo "Notificación enviada correctamente."
                              }
                         }
                    }
               }
          }
     }
}
