pipeline {
    agent any

    stages {
        stage('Inicio del juego') {
            steps {
                echo 'Preparando entorno inicial...'
            }
        }

        stage('Clonar el universo') {
            steps {
                git url: 'https://github.com/antoja97/unir_ex1', branch: 'develop'
                echo 'Código descargado del repositorio.'
            }
        }

        stage('Explorar terreno') {
            steps {
                sh 'ls -la'
                echo "Exploración completa del directorio."
            }
        }

        stage('Dónde estoy') {
            steps {
                echo "Estoy en: ${env.WORKSPACE}"
                sh 'echo "Usuario que ejecuta: $(whoami)"'
                sh 'echo "Hostname: $(hostname)"'
            }
        }

        stage('Instalar dependencias') {
            steps {
                script {
                    sh 'pip install -r requirements.txt'
                }
            }
        }

        stage('Ejecutar pruebas') {
            steps {
                script {
                    sh 'pytest --maxfail=5 --disable-warnings -q'
                }
            }
        }

        stage('Construcción (simbólica)') {
            steps {
                echo 'Simulación de build completada.'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado'
        }
        success {
            echo 'El pipeline se ejecutó correctamente.'
        }
        failure {
            echo 'El pipeline ha fallado.'
        }
    }
}
