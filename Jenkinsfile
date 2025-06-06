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
                    def flake8Output = ''
                    def flake8Issues = 0
                    try {
                        flake8Output = bat(script: "python -m flake8 .", returnStdout: true).trim()
                    } catch (err) {
                        flake8Output = err.getMessage()
                    }
                    if (flake8Output) {
                        flake8Issues = flake8Output.readLines().size()
                    }
                    echo "Flake8 hallazgos: ${flake8Issues}"
                    if (flake8Issues >= 10) {
                        currentBuild.result = 'FAILURE'
                        echo "Flake8: 10 o más hallazgos - build marcada como FAILURE"
                    } else if (flake8Issues >= 8) {
                        if (currentBuild.result != 'FAILURE') {
                            currentBuild.result = 'UNSTABLE'
                        }
                        echo "Flake8: entre 8 y 9 hallazgos - build marcada como UNSTABLE"
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
                    def banditOutput = ''
                    def banditIssues = 0
                    try {
                        banditOutput = bat(script: 'python -m bandit -r . -f json', returnStdout: true).trim()
                    } catch (err) {
                        banditOutput = err.getMessage()
                    }
                    if (banditOutput) {
                        def json = readJSON text: banditOutput
                        banditIssues = json.metrics._totals.total_issues ?: 0
                    }
                    echo "Bandit hallazgos: ${banditIssues}"
                    if (banditIssues >= 4) {
                        currentBuild.result = 'FAILURE'
                        echo "Bandit: 4 o más hallazgos - build marcada como FAILURE"
                    } else if (banditIssues >= 2) {
                        if (currentBuild.result != 'FAILURE') {
                            currentBuild.result = 'UNSTABLE'
                        }
                        echo "Bandit: entre 2 y 3 hallazgos - build marcada como UNSTABLE"
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
                echo 'Ejecutando pruebas unitarias y reporte de cobertura...'
                script {
                    def covRunOut = ''
                    def covReportOut = ''
                    try {
                        covRunOut = bat(script: 'python -m coverage run -m unittest discover', returnStdout: true).trim()
                        covReportOut = bat(script: 'python -m coverage report -m', returnStdout: true).trim()
                    } catch (err) {
                        echo "Error ejecutando cobertura: ${err}"
                    }
                    echo covReportOut
                    def lineCoverage = 0
                    def branchCoverage = 0
                    covReportOut.readLines().each { line ->
                        if (line =~ /^TOTAL/) {
                            def parts = line.trim().split(/\s+/)
                            if (parts.length >= 5) {
                                lineCoverage = parts[3].replace('%', '').toFloat()
                                branchCoverage = parts[4].replace('%', '').toFloat()
                            }
                        }
                    }
                    echo "Cobertura líneas: ${lineCoverage}%, Cobertura ramas: ${branchCoverage}%"
                    if (lineCoverage < 85 || branchCoverage < 80) {
                        currentBuild.result = 'FAILURE'
                        echo "Cobertura insuficiente - build marcada como FAILURE"
                    } else if ((lineCoverage >= 85 && lineCoverage < 95) || (branchCoverage >= 80 && branchCoverage < 90)) {
                        if (currentBuild.result != 'FAILURE') {
                            currentBuild.result = 'UNSTABLE'
                        }
                        echo "Cobertura en rango inestable - build marcada como UNSTABLE"
                    }
                }
            }
            post {
                always {
                    echo 'Continuando pipeline tras pruebas de cobertura y unitarias.'
                }
            }
        }

        stage('Performance') {
            steps {
                echo 'Corriendo pruebas de rendimiento con JMeter...'
                bat 'jmeter -n -t tests/performance_test.jmx -l results.jtl'
            }
        }
    }
}
