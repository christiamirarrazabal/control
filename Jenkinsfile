def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE':'danger', 
    ]


pipeline {
    agent any 

    tools { 
        maven 'jenkinsmaven'
        jdk 'java17'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MiguelAngelRamos/control.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }


stage('Sonar Scanner') {
    steps {
        withSonarQubeEnv('SonarQube') { 
            sh 'mvn sonar:sonar -Dsonar.projectKey=GS -Dsonar.sources=src/main/java/com/kibernumacademy/miapp -Dsonar.tests=src/test/java/com/kibernumacademy/miapp -Dsonar.java.binaries=.'
        }
    }
}

        stage('Quality Gate'){
            steps{
                timeout(time:1, unit:'HOURS'){
                    waitForQualityGate abortPipeline:true
                }
            }
        }

    }

    post{
        always{
            echo 'Slack Notification'
            slackSend channel: '#alertas',
            color: COLOR_MAP[currentBuild.currentResult], 
            message: "*${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More Info at: ${env.BUILD_URL}"
        }
    }
}
