pipeline {
    agent {
        label none
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
    }

    stages {
        stage('Unit tests') {
            agent {
                label 'apache'
            }

            steps {
                sh 'ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }

        stage('build') {
            agent {
                label 'apache'
            }

            steps {
                sh 'ant -f build.xml -v'
            }
        }

        stage('deploy') {
            agent {
                label 'apache'
            }

            steps {
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
            }
        }

        stage("Running on slave"){
            agent {
                label 'slave'    
            }
            steps {
                sh "wget http://centos7-01/rectangles/all/rectangle_${BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${BUILD_NUMBER}.jar 3 7"
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
    }
}
