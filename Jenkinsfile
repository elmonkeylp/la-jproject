pipeline {

    agent none

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

            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                }
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

        stage("Test on Debian"){
            agent{
                docker'openjdk:10.0.1-10-jre'
            }
            steps {
                sh "wget http://192.168.1.201/rectangles/all/rectangle_${BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${BUILD_NUMBER}.jar 2 4"
            }
        }
    }
}
