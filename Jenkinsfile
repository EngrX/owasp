pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn clean install'
            }
        }

        stage('Dependency Check') {
            steps {
                // Run OWASP Dependency-Check analysis
                dependencyCheck additionalArguments: '--scan . --format HTML', odcInstallation: 'DependencyCheck'
            }
        }

        stage('Publish Report') {
            steps {
                // Publish the report generated
                dependencyCheckPublisher pattern: '**/dependency-check-report.html'
            }
        }
    }
}