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
                    sh 'CHROME_BIN=/usr/bin/chromium npm run test -- --watch=false --browsers=ChromeHeadlessCI'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('backend') {
                    withSonarQubeEnv('SonarQube') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                dir('backend') {
                    withMaven(globalMavenSettingsConfig: 'bc716260-f2bc-45fb-809a-a9cbaee3cc4d') {
                        sh 'mvn deploy -DskipTests'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline complet reussi jusqu a Nexus !'
        }
        failure {
            echo 'Le pipeline a echoue.'
        }
    }
}
