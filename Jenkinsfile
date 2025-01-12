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
     }
}
