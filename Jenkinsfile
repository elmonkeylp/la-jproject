pipeline {

    agent none

    env {
        MAJOR_VERSION = 1
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '1'))
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

        stage('Build') {
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

        stage('Deploy') {
            agent {
                label 'apache'
            }

            steps {
                sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}']; then mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
                sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${BRANCH_NAME}/"
            }
        }

        stage("Running on slave"){
            agent {
                label 'slave'    
            }
            steps {
                sh "wget http://centos7-01/rectangles/all/${BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 7"
            }
        }

        stage("Test on Debian"){
            agent{
                docker'openjdk:10.0.1-10-jre'
            }
            steps {
                sh "wget http://192.168.1.201/rectangles/all/${BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 2 4"
            }
        }

        stage("Promote to Green") {
            agent {
                label 'apache'
            }
            when {
                branch 'master'
            }
            steps {
                sh "cp /var/www/html/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
            }
        }

        stage ("Promot Development Branch to Master") {
            agent {
                label 'apache'
            }
            when {
                branch 'development'
            }
            steps {
                echo "Stashing Any Local Changes"
                sh 'git stash'
                echo "Checking Out Development Branch"
                sh 'git checkout development'
                echo "Checking Out Master Branch"
                sh 'git pull'
                sh 'git checkout master'
                echo "Merging Development into Master Branch"
                sh 'git merge development'
                echo "Pushing to Master"
                sh 'git push origin master'
                echo "Tagging the Release"
                sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
                sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
            }
        }
    }
}
