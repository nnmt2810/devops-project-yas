pipeline {
    agent any

    tools {
        jdk 'jdk-21'
        maven 'Maven-3'
    }

    environment {
        DOCKERHUB_USER = 'nnminhtuan2810'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                script {
                    env.COMMIT_ID = sh(
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()
                    echo "Commit ID: ${env.COMMIT_ID}"
                    echo "Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Maven Build') {
            parallel {
                stage('Build cart') {
                    when { changeset "cart/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl cart -am'
                    }
                }
                stage('Build product') {
                    when { changeset "product/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl product -am'
                    }
                }
                stage('Build order') {
                    when { changeset "order/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl order -am'
                    }
                }
                stage('Build customer') {
                    when { changeset "customer/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl customer -am'
                    }
                }
                stage('Build tax') {
                    when { changeset "tax/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl tax -am'
                    }
                }
                stage('Build inventory') {
                    when { changeset "inventory/**" }
                    steps {
                        sh 'mvn -B clean package -DskipTests -pl inventory -am'
                    }
                }
            }
        }

        stage('Build & Push Docker Images') {
            parallel {
                stage('cart') {
                    when { changeset "cart/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-cart:${env.COMMIT_ID}", "./cart")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
                stage('product') {
                    when { changeset "product/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-product:${env.COMMIT_ID}", "./product")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
                stage('order') {
                    when { changeset "order/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-order:${env.COMMIT_ID}", "./order")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
                stage('customer') {
                    when { changeset "customer/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-customer:${env.COMMIT_ID}", "./customer")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
                stage('tax') {
                    when { changeset "tax/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-tax:${env.COMMIT_ID}", "./tax")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
                stage('inventory') {
                    when { changeset "inventory/**" }
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                                def img = docker.build("${DOCKERHUB_USER}/yas-inventory:${env.COMMIT_ID}", "./inventory")
                                img.push()
                                if (env.BRANCH_NAME == 'main') { img.push('main') }
                            }
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build success - Image tag: ${env.COMMIT_ID}"
        }
        failure {
            echo "❌ Build failed"
        }
    }
}
