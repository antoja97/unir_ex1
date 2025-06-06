pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                git url: 'https://github.com/antoja97/unir_ex1', branch: 'master'
                echo 'Código descargado, listo para seguir.'
            }
        }

        stage('Static Analysis') {
            steps {
                echo 'Ejecutando análisis estático con flake8...'
                script {
                    def flake8Output = bat(script: 'python -m flake8 .', returnStdout: true).trim()
                    def issues = 0
                    if (flake8Output) {
                        issues = flake8Output.readLines().size()
                    }
                    echo "Flake8 hallazgos: ${issues}"
                    if (issues >= 10) {
                        currentBuild.result = 'FAILURE'
                        error("Demasiados hallazgos flake8 (${issues}), build unhealthy")
                    } else if (issues >= 8) {
                        unstable("Flake8 hallazgos >= 8, marcando build como UNSTABLE")
                    }
                }
            }
            post {
                always {
                    echo 'Continuando pipeline tras análisis estático.'
                }
            }
        }

        stage('Security Test') {
            steps {
                echo 'Lanzando pruebas de seguridad con bandit...'
                script {
                    def banditOutput = bat(script: 'python -m bandit -r . -f json', returnStdout: true).trim()
                    def json = readJSON text: banditOutput
                    def issues = json['results'].size()
                    echo "Bandit hallazgos: ${issues}"
                    if (issues >= 4) {
                        currentBuild.result = 'FAILURE'
                        error("Demasiados hallazgos bandit (${issues}), build unhealthy")
                    } else if (issues >= 2) {
                        unstable("Bandit hallazgos >= 2, marcando build como UNSTABLE")
                    }
                }
            }
            post {
                always {
                    echo 'Continuando pipeline tras pruebas de seguridad.'
                }
            }
        }

        stage('Coverage and Unit Tests') {
            steps {
                echo 'Ejecutando pruebas unitarias y coverage...'
                script {
                    bat 'python -m coverage run -m unittest discover'
                    def report = bat(script: 'python -m coverage report --fail-under=0 -m', returnStdout: true).trim()
                    echo report
                    def lineCoverage = 0.0
                    def branchCoverage = 0.0
                    report.readLines().each { line ->
                        if (line.startsWith("TOTAL")) {
                            def parts = line.trim().split(/\s+/)
                            lineCoverage = parts[3].replace('%','').toFloat()
                            branchCoverage = parts.length > 4 ? parts[4].replace('%','').toFloat() : 100.0
                        }
                    }
                    echo "Cobertura línea: ${lineCoverage}%, cobertura rama: ${branchCoverage}%"
                    if (lineCoverage < 85 || branchCoverage < 80) {
                        currentBuild.result = 'FAILURE'
                        error("Cobertura insuficiente: línea < 85% o rama < 80%")
                    } else if ((lineCoverage >= 85 && lineCoverage < 95) || (branchCoverage >= 80 && branchCoverage < 90)) {
                        unstable("Cobertura en rango warning: líneas entre 85 y 95, o ramas entre 80 y 90")
                    }
                }
            }
            post {
                always {
                    echo 'Continuando pipeline tras cobertura y tests unitarios.'
                }
            }
        }

        stage('Performance') {
            steps {
                echo 'Ejecutando pruebas de rendimiento con JMeter...'
                script {
                    try {
                        bat 'jmeter -n -t tests/performance_test.jmx -l results.jtl'
                    } catch (e) {
                        unstable("Fallo JMeter, pero se continúa pipeline")
                    }
                }
            }
        }
    }
}
