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
                sh 'docker build -t list-build:latest -f Dockerfile.build .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker build -t list-test -f Dockerfile.test .'
                sh 'docker run --rm list-test'
            }
        }

       stage('Deploy') {
    steps {
        sh 'docker build -t list-deploy -f Dockerfile.deploy .'
       
        sh 'docker create --name temp-container list-deploy'
        sh 'docker cp temp-container:/artifact/libclibs_list.a ./libclibs_list.a'
        sh 'docker rm temp-container'
        
        sh 'docker run --rm list-deploy'
        
        archiveArtifacts artifacts: 'libclibs_list.a', fingerprint: true
    }
}

        stage('Publish') {
    steps {
        script {
            docker.withRegistry('https://index.docker.io/v1/', 'registry-credentials') {
                def image = docker.build("al5ksandra/list:${env.BUILD_NUMBER}", "-f Dockerfile.deploy .")
                image.push()
                image.push('latest')
            }
        }
    }
}
    }
}
