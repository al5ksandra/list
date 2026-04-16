pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Budujemy obraz budujący i tagujemy go jako 'list-build'
                    sh 'docker build -t list-build:latest -f Dockerfile.build .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Uruchamiamy testy korzystając z Dockerfile.test
                    sh 'docker build -t list-test -f Dockerfile.test .'
                    sh 'docker run --rm list-test'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Przygotowanie artefaktu (biblioteki .a)
                    sh 'docker build -t list-deploy -f Dockerfile.deploy .'
                    sh 'docker run --rm list-deploy'
                }
            }
        }
    }
}
