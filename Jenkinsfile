pipeline {
    agent any

    environment {
        IMAGE_BUILD = "list-build:${BUILD_NUMBER}"
        IMAGE_TEST = "list-test:${BUILD_NUMBER}"
        IMAGE_DEPLOY = "list-deploy:${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'AP421868',
                    url: 'https://github.com/InzynieriaOprogramowaniaAGH/MDO2026_ITE.git'
            }
        }

        stage('Build') {
            steps {
                dir('grupa4/AP421868') {
                    sh 'docker build -t ${IMAGE_BUILD} -f Dockerfile.build . > build-${BUILD_NUMBER}.log 2>&1'
                }
            }
        }

        stage('Test') {
            steps {
                dir('grupa4/AP421868') {
                    sh 'docker build -t ${IMAGE_TEST} -f Dockerfile.test . > test-build-${BUILD_NUMBER}.log 2>&1'
                    sh 'docker run --rm ${IMAGE_TEST} > test-run-${BUILD_NUMBER}.log 2>&1'
                }
            }
        }

        stage('Prepare Artifact') {
            steps {
                dir('grupa4/AP421868') {
                    sh '''
                        docker create --name tmp-${BUILD_NUMBER} ${IMAGE_BUILD}
                        docker cp tmp-${BUILD_NUMBER}:/build/build/libclibs_list.a ./libclibs_list.a
                        docker rm -f tmp-${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('grupa4/AP421868') {
                    sh 'docker build -t ${IMAGE_DEPLOY} -f Dockerfile.deploy . > deploy-${BUILD_NUMBER}.log 2>&1'
                }
            }
        }

        stage('Smoke Test') {
            steps {
                dir('grupa4/AP421868') {
                    sh 'docker run --rm ${IMAGE_DEPLOY} > smoke-${BUILD_NUMBER}.log 2>&1'
                }
            }
        }

        stage('Publish') {
            steps {
                dir('grupa4/AP421868') {
                    archiveArtifacts artifacts: 'libclibs_list.a, *.log', fingerprint: true
                }
            }
        }
    }
}
