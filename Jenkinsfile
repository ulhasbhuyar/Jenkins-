pipeline {
    agent any

    stages {
        stage('Build Maven') {
            steps {
                script {
                    sh 'pwd'
                    sh 'mvn clean install package'
                }
            }
        }

        stage('Copy Artifacts') {
            steps {
                script {
                    sh 'pwd'
                    sh 'cp -r target/*.jar docker'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def customImage = docker.build("ulhasbhuyar/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubeconfig']) {
                        sh 'pwd'
                        sh 'cp -R helm/* .'
                        sh 'ls -ltrh'
                        sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=ulhasbhuyar/petclinic --set image.tag=${BUILD_NUMBER}'
                    }
                }
            }
        }
    }
}
