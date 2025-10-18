y plugin
🧱 Then in your Jenkinsfile:
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
                dependencyCheck additionalArguments: '--scan . --format HTML', odcInstallation: 'DependencyCheck'
            }
        }

        stage('Publish Report') {
            steps {
                dependencyCheckPublisher pattern: '**/dependency-check-report.html'
            }
        }
    }
}