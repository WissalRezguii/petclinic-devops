pipeline {
    agent any

    tools {
        nodejs 'NodeJS_24'
        maven 'Maven_3'
        jdk 'JDK_17'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/WissalRezguii/petclinic-devops.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build -- --configuration production'
                }
            }
        }

        stage('Tests Backend') {
            steps {
                dir('backend') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    junit 'backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Tests Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm run test -- --watch=false --browsers=ChromeHeadless'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build et Tests réussis !'
        }
        failure {
            echo '❌ Le pipeline a échoué.'
        }
    }
}
