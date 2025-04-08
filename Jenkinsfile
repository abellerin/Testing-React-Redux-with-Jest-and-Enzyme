pipeline {
    agent any

    environment {
        TIMESTAMP = "${new Date().format('yyyyMMdd_HHmmss')}"
        OUTPUT_DIR = "seguridad_${TIMESTAMP}"
    }

    stages {

        stage('Preparar directorio') {
            steps {
                sh 'mkdir -p $OUTPUT_DIR'
            }
        }

        stage('Obtener versiones') {
            steps {
                sh '''
                    echo "Versión de Java:" > $OUTPUT_DIR/versiones_$TIMESTAMP.txt
                    java -version 2>> $OUTPUT_DIR/versiones_$TIMESTAMP.txt

                    echo "\\nVersión de Jenkins:" >> $OUTPUT_DIR/versiones_$TIMESTAMP.txt
                    curl -s http://localhost:8080/api/json?pretty=true | grep "version" >> $OUTPUT_DIR/versiones_$TIMESTAMP.txt
                '''
            }
        }

        stage('Escanear IP y puertos abiertos') {
            steps {
                sh '''
                    IP=$(hostname -I | awk '{print $1}')
                    nmap -Pn -sS $IP -oN $OUTPUT_DIR/puertos_$TIMESTAMP.txt
                '''
            }
        }

        stage('Obtener hashes SHA-256') {
            steps {
                sh '''
                    HASH_OUTPUT="$OUTPUT_DIR/hashes_$TIMESTAMP.txt"
                    RUTA="/var/jenkins_home/workspace/tuJob/subfolder/project"

                    if [ -d "$RUTA" ]; then
                        find $RUTA -type f -exec sha256sum {} \\; > $HASH_OUTPUT
                    else
                        echo "La ruta $RUTA no existe" > $HASH_OUTPUT
                    fi
                '''
            }
        }

        stage('Archivar resultados') {
            steps {
                archiveArtifacts artifacts: '$OUTPUT_DIR/*.txt', fingerprint: true
            }
        }
    }
}
