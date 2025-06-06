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
                bat 'python -m flake8 .'
            }
        }

        stage('Security Test') {
            steps {
                echo 'Lanzando pruebas de seguridad con bandit...'
                bat 'python -m bandit -r .'
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
                bat 'python -m coverage run -m unittest discover'
                bat 'python -m coverage report -m'
            }
        }
    }
}
