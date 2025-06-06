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
                sh 'flake8 .'
            }
        }

        stage('Security Test') {
            steps {
                echo 'Lanzando pruebas de seguridad con bandit...'
                sh 'bandit -r .'
            }
        }

        stage('Performance') {
            steps {
                echo 'Corriendo pruebas de rendimiento con JMeter...'
                sh 'jmeter -n -t tests/performance_test.jmx -l results.jtl'
            }
        }

        stage('Coverage') {
            steps {
                echo 'Generando reporte de cobertura con coverage...'
                sh 'coverage run -m unittest discover'
                sh 'coverage report -m'
            }
        }
    }
}
