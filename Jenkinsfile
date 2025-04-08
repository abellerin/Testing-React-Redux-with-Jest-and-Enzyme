pipeline {
    agent any
    environment {
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
    stages {
        stage('Versiones') {
            steps {
                script {
                    def filename = "versiones_${TIMESTAMP}.txt"
                    sh """
                        echo "Java Version:" >> ${filename}
                        java -version 2>> ${filename}
                        echo "\\nJenkins Version:" >> ${filename}
                        curl -I -s http://\$(ip route | awk '/default/ { print \$3 }'):8081 | grep -i X-Jenkins >> ${filename} || true
                    """

                    // Guardar como artefacto descargable
                    archiveArtifacts artifacts: filename, fingerprint: true
                }
            }
        }
        stage('Escaneo de Puertos') {
            steps {
                script {
                    def filename = "puertos_${TIMESTAMP}.txt"
                    def jenkinsIP = sh(script: "hostname -I | awk '{print \$1}'", returnStdout: true).trim()
                    sh """
                        echo "\\nJenkins Version:" >> ${filename}
                        curl -I -s http://${jenkinsIP}:8081 | grep -i X-Jenkins >> ${filename} || true
                    """

                    // Guardar como artefacto descargable
                    archiveArtifacts artifacts: filename, fingerprint: true
                }
            }
        }
        stage('Checksums SHA-256') {
            steps {
                script {
                    def filename = "hashes_${TIMESTAMP}.txt"
                    def ruta = "${env.JENKINS_HOME}/workspace/securityPipeline"
                    sh """
                        find ${ruta} -type f -exec sha256sum {} \\; > ${filename}
                    """
                    // Guardar como artefacto descargable
                    archiveArtifacts artifacts: filename, fingerprint: true
                }
            }
        }
        stage('Comparación de Hashes (opcional)') {
            when {
                expression {
                    // Solo ejecutar si hay un archivo anterior
                    return fileExists("hashes_previo.txt")
                }
            }
            steps {
                script {
                    def filename = "comparacion_hashes.txt"
                    def actual = "hashes_${TIMESTAMP}.txt"
                    sh """
                        echo "Comparación entre hashes_previo.txt y ${actual}:" > ${filename}
                        diff hashes_previo.txt ${actual} >> ${filename} || echo "Cambios detectados"
                    """
                    // Guardar como artefacto descargable
                    archiveArtifacts artifacts: filename, fingerprint: true
                }
            }
        }

    }

    post {
        always {
            // Guardar el archivo actual como base para futura comparación
            sh "cp hashes_${TIMESTAMP}.txt hashes_previo.txt"
        }
    }
}
