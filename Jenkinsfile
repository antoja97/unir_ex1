pipeline {
    agent any

    environment {
        PYTHONPATH = "${WORKSPACE}/helloworld-master"
    }

    stages {
        stage('Inicio del juego') {
            steps {
                echo 'Preparando entorno inicial...'
            }
        }

        stage('Clonar el universo') {
            steps {
                echo 'Clonando el repositorio...'
                git 'https://github.com/antoja97/unir_ex1'
            }
        }

        stage('Explorar terreno') {
            steps {
                echo 'Explorando el directorio...'
                sh 'ls -la'
            }
        }

        stage('Dónde estoy') {
            steps {
                echo "Estoy en: ${WORKSPACE}"
                sh 'whoami'
                sh 'hostname'
            }
        }

        stage('Construcción (simbólica)') {
            steps {
                echo 'Simulación de build completada.'
            }
        }

        stage('Ejecutar Tests Unitarios') {
            steps {
                echo 'Ejecutando pruebas unitarias...'
                sh 'pytest helloworld-master/test/unit'
            }
        }

        stage('Ejecutar Tests de Integración') {
            steps {
                echo 'Ejecutando pruebas de integración...'
                sh 'pytest helloworld-master/test/rest'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
        }

        success {
            echo 'Pipeline ejecutado correctamente.'
        }

        failure {
            echo 'Hubo un error en la ejecución del pipeline.'
        }
    }
}
