pipeline {
    agent none

    stages {
        stage ('build') {
            agent {
                label 'slave'
            }
            steps {
                sh 'ant -f build.xml -v'
            }
        }
    }
}
