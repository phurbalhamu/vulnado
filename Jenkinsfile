pipeline {
    agent any

    stages {


stage('Check-Git-Secrets') {
    steps {
        sh 'rm -f trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/cekhunal/webapp.git > trufflehog'
        sh 'cat trufflehog'
    }
}


stage('Source-Composition-Analysis') {
    steps {
        sh '''
        rm -f owasp*
        wget https://raw.githubusercontent.com/devopssecure/webapp/master/owasp-dependency-check.sh
        chmod +x owasp-dependency-check.sh
        ./owasp-dependency-check.sh
        '''
    }
}
    

        stage('Build') {
            steps {
                echo 'Building the project'
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application'
            }
        }
    }
}
