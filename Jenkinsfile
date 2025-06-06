pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                git url: 'https://github.com/antoja97/unir_ex1', branch: 'master'
                echo 'Código descargado, listo para seguir.'
            }
        }

        stage('Static') {
            steps {
                echo 'Ejecutando análisis estático con flake8...'
                bat 'flake8 .'
            }
        }

        stage('Security Test') {
            steps {
                echo 'Lanzando pruebas de seguridad con bandit...'
                bat 'bandit -r .'
            }
        }

        stage('Performance') {
            steps {
                echo 'Corriendo pruebas de rendimiento con JMeter...'
                bat 'jmeter -n -t tests/performance_test.jmx -l results.jtl'
            }
        }

        stage('Coverage') {
            steps {
                echo 'Generando reporte de cobertura con coverage...'
                bat 'coverage run -m unittest discover'
                bat 'coverage report -m'
            }
        }
    }
}
