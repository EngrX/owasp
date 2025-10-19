pipeline {
    agent any
    tools {
        jdk 'JDK17'
        maven 'Maven'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Dependency Check') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'ossIndexCreds', usernameVariable: 'OSS_USER', passwordVariable: 'OSS_TOKEN')]) {
                    sh """
                    /opt/dependency-check/bin/dependency-check.sh \
                        --project "MyProject" \
                        --scan . \
                        --format HTML \
                        --noupdate \
                        --enableExperimental \
                        --ossIndexUser $OSS_USER \
                        --ossIndexToken $OSS_TOKEN \
                        --failOnCVSS 7 \
                        --out $WORKSPACE/dependency-check-report.html
                    """
                }
            }
        }

        stage('Publish Report') {
            steps {
                dependencyCheckPublisher pattern: '**/dependency-check-report.html'
                archiveArtifacts artifacts: 'dependency-check-report.html', allowEmptyArchive: true
            }
        }
    }
}