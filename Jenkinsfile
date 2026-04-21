pipeline {
    agent any

    stages {
        stage('Prepare') {
            steps {
               
                cleanWs()
                
                checkout scm
                echo 'Workspace wyczyszczony i kod pobrany ponownie.'
            }
        }

        stage('Build') {
            steps {
                script {
                   
                    sh 'docker build -t list-build:latest -f Dockerfile.build .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'docker build -t list-test -f Dockerfile.test .'
                    sh 'docker run --rm list-test'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker build -t list-deploy -f Dockerfile.deploy .'
                    sh 'docker run --rm list-deploy'
                }
            }
        }
        stage('Publish') {
    steps {
        script {
            docker.withRegistry('https://registry.example.com', 'registry-credentials') {
                def image = docker.build("registry.example.com/list:${env.BUILD_NUMBER}", "-f Dockerfile.deploy .")
                image.push()
                image.push('latest')
            }
        }
    }
}
        
    }


}
