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
                    sh '''
                    export OSSINDEX_USER=$OSS_USER
                    export OSSINDEX_TOKEN=$OSS_TOKEN

                    /opt/dependency-check/bin/dependency-check.sh \
                        --project "MyProject" \
                        --scan . \
                        --format HTML \
                        --noupdate \
                        --failOnCVSS 7 \
                        --out $WORKSPACE/dependency-check-report.html
                    '''
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