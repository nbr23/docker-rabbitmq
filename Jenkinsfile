pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    stages {
        stage('Checkout'){
            steps {
                checkout scm
            }
        }
        stage('Prep buildx') {
            steps {
                script {
                    env.BUILDX_BUILDER = getBuildxBuilder();
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    [
                        [
                            version: '3.11.18-alpine',
                            path: '3.11/alpine'
                        ],
                        [
                            version: '3.11.18-management-alpine',
                            path: '3.11/alpine/management'
                        ],
                        [
                            version: '3.12.6-alpine',
                            path: '3.12/alpine'
                        ],
                        [
                            version: '3.12.6-management-alpine',
                            path: '3.12/alpine/management'
                        ],
                    ].each{ v ->
                        sh "docker buildx build --pull --push --platform linux/amd64,linux/arm64 --builder \$BUILDX_BUILDER -t nbr23/rabbitmq:$v.version $v.path"
                    }
                }
            }
        }
    }
    post {
        always {
            sh 'docker buildx stop $BUILDX_BUILDER || true'
            sh 'docker buildx rm $BUILDX_BUILDER || true'
        }
    }
}
