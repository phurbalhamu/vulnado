pipeline {
    agent any

    stages {

stage('Check-Git-Secrets') {
    steps {
        sh '''
          rm -f trufflehog.json

          docker run --rm \
            -v "$PWD:/work" \
            -w /work \
            ghcr.io/trufflesecurity/trufflehog:latest \
            filesystem /work \
            --json \
            --no-update \
            > trufflehog.json

          echo "==== FILE EXISTS? ===="
          ls -lh trufflehog.json || true

          echo "==== FILE CONTENT (JSON) ===="
          cat trufflehog.json || true
        '''
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
