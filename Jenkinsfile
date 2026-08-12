pipeline {

    agent any

    stages {

        stage('Validate') {
            steps {
                echo 'Validando estructura del proyecto...'

                sh 'test -f Dockerfile'
                sh 'test -f compose.yaml'
                sh 'test -f index.html'
                sh 'test -f environments/dev/compose.yaml'

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

        stage('Docker Test') {
            steps {
                echo 'Iniciando contenedor temporal para pruebas...'

                sh '''
                    docker run -d \
                        --name techstore-test \
                        --network jenkins \
                        techstore-web:${BUILD_NUMBER}
                '''

                echo 'Esperando que Nginx esté disponible...'

                sh '''
                    sleep 3
                    curl -f http://techstore-test
                '''
            }

            post {
                always {
                    echo 'Eliminando contenedor temporal...'
                    sh 'docker rm -f techstore-test || true'
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Desplegando nueva versión en DEV...'

                sh '''
                    WEB_IMAGE=techstore-web:${BUILD_NUMBER} \
                    docker compose \
                    -f environments/dev/compose.yaml \
                    -p techstore-dev \
                    up -d
                '''

                echo 'Despliegue en DEV completado correctamente.'
            }
        }

    }

    post {

        success {
            echo 'Pipeline ejecutado correctamente.'
        }

        failure {
            echo 'El pipeline ha fallado.'
        }

    }
}
