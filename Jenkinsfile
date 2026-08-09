pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Obteniendo código desde GitHub...'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Validando estructura del proyecto...'

                sh 'test -f Dockerfile'
                sh 'test -f compose.yaml'
                sh 'test -f index.html'

                echo 'Validación completada correctamente.'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Construyendo imagen Docker...'

                sh 'docker version'
                sh 'docker build -t techstore-web:${BUILD_NUMBER} .'

                echo 'Imagen Docker construida correctamente.'
            }
        }

    }

    post {
        success {
            echo '🚀 Pipeline ejecutado correctamente.'
        }

        failure {
            echo '❌ El pipeline ha fallado.'
        }
    }
}
