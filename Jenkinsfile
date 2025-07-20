pipeline {
    agent any

    environment {
        APP_PORT = '9090'
        JOB_NAME = "${env.JOB_NAME}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Integration Test') {
            parallel {
                stage('Running Application') {
                    steps {
                        script {
                            try {
                                timeout(time: 1, unit: 'MINUTES') {
                                    dir('target') {
                                        sh 'java -jar -Dserver.port=${APP_PORT} contact.war &'
                                        sleep 60
                                    }
                                }
                            } catch (ERROR) {
                                echo 'Success.'
                            }
                        }
                    }
                }

                stage('Running Test') {
                    steps {
                        sh "sleep 30"
                        sh "mvn -Dtest=RestIT test"
                    }
                }
            }
        }        
    }
}
