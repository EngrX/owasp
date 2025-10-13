pipeline {
    agent {
        label 'agent-1'
    }

    environment {
        // Adjust these paths based on where Dependency-Check CLI is installed on your agent
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${PATH}"
        DEP_CHECK_HOME = "/opt/dependency-check"
        DEP_CHECK_DATA = "${DEP_CHECK_HOME}/data"
        REPORT_DIR = "dependency-check-report"
        // Optional: NVD API Key stored in Jenkins credentials
        // NVD_API_KEY = credentials('nvd-api-key')
        NVD_API_KEY = credentials('nvd-api-key')
        
    }

    tools {
        maven 'Maven_3_9' // Jenkins → Manage Jenkins → Tools
        jdk 'JDK17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Application') {
            steps {
                sh '''
                    echo "Using Java Home: $JAVA_HOME"
                    echo "PATH: $PATH"
                    java -version
                    mvn -version
                    mvn clean package -DskipTests
                '''
            }
        }

        stage('OWASP Dependency Check (CLI)') {
            steps {
                sh """
                    mkdir -p ${REPORT_DIR}
                    ${DEP_CHECK_HOME}/bin/dependency-check.sh \
                        --project "Java OWASP Demo" \
                        --scan . \
                        --data ${DEP_CHECK_DATA} \
                        --format HTML \
                        --format XML \
                        --out ${REPORT_DIR} \
                        --nvdApiKey ${NVD_API_KEY}
                """
            }
        }

        stage('Publish OWASP Report') {
            steps {
                publishHTML(target: [
                    reportName: 'OWASP Dependency Check Report',
                    reportDir: "${REPORT_DIR}",
                    reportFiles: 'dependency-check-report.html',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])

                archiveArtifacts artifacts: "${REPORT_DIR}/dependency-check-report.*", fingerprint: true
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline finished. OWASP report archived and published."
        }
    }
}