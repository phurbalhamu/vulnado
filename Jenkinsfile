pipeline {
    agent any

    stages {


stage('Check-Git-Secrets') {
    steps {
        sh 'rm -f trufflehog.json || true'
        sh '''
          docker run --rm \
            -v "$PWD:/work" \
            -w /work \
            gesellix/trufflehog \
            sh -c "trufflehog filesystem . --json > trufflehog.json"
        '''
        sh 'ls -l trufflehog.json'
        sh 'cat trufflehog.json'
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
