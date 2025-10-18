pipeline {
    agent any
    tools {
        dependencyCheck 'DependencyCheck' // must match the name you added under Global Tool Config
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
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