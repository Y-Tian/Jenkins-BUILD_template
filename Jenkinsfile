pipeline {
    agent { label '<LABEL_NAME>' }
    environment {
        DOCKER_AUTH = credentials('<CREDENTIALS_HERE>')
        REPO = "<REPO_NAME>"
        VERSION = sh ( script: 'TZ=UTC date +%Y%m%d%H%M%S', returnStdout: true )
    }
    stages {
        stage('Pre-build') {
            steps {
                sh 'docker login -u $DOCKER_AUTH_USR -p $DOCKER_AUTH_PSW $REPO'
                cleanWs()
                script { 
                    currentBuild.displayName = "$VERSION"
                }
                //clones repo
                checkout scm
            }
        }
        stage('Build Images') {
            steps {
                sh "./build.sh"
            }
        }
        stage('Tag Images') {
            steps {
                sh "./tag.sh"
            }
        }
        stage('Test Images') {
            steps {
                sh "./test.sh"
            }
        }
        stage('Push Images') {
            when {
                branch 'master'
            }
            steps {
                sh "./push.sh"
            }
        }
    }
    post {
        always {
            sh 'docker logout $REPO'
        }
    }
}
