pipeline {
    agent any
    tools { maven 'Maven3' }          // Manage Jenkins -> Tools -> Maven installations (name: Maven3)
    options { timestamps(); timeout(time: 20, unit: 'MINUTES'); buildDiscarder(logRotator(numToKeepStr: '10')) }
    triggers { pollSCM('H/2 * * * *') }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps { sh 'mvn -B clean compile' }
        }
        stage('Test') {
            steps { sh 'mvn -B test' }
            post { always { junit 'target/surefire-reports/*.xml' } }
        }
        stage('Package') {
            steps { sh 'mvn -B package -DskipTests' }
        }
        stage('Archive') {
            steps { archiveArtifacts artifacts: 'target/*.jar', fingerprint: true }
        }
        stage('Deploy') {
            when { branch 'main' }
            steps { echo 'Deploying artifact (copy to server / push to Nexus) ...' }
        }
    }
    post {
        success { echo 'Pipeline SUCCESS' }
        failure { echo 'Pipeline FAILED' }
        always  { cleanWs() }
    }
}
