pipeline {
    agent {
        label 'slave'
    }

    stages {
        stage ('build') {
            steps {
                sh 'ant -f build.xml -v'
            }
        }
    }

    post {
        always {
            archive 'dist/*.jar'
        }
    }
}
