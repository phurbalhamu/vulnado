pipeline {
    agent any

    stages {

stage('Check-Git-Secrets') {
    steps {
        script {
            sh '''
                # 1. Clear old reports
                rm -f trufflehog.json

                # 2. Run TruffleHog
                # We use || true or set +e so the script doesn't stop if secrets are found
                set +e
                docker run --rm \
                    -v "$WORKSPACE:/work" \
                    -w /work \
                    ghcr.io/trufflesecurity/trufflehog:latest \
                    filesystem /work \
                    --json \
                    --no-update > trufflehog.json
                
                # 3. Capture the exit code (1 means secrets found, 0 means clean)
                TRUFFLE_EXIT=$?
                set -e

                # 4. Debug: Show the file exists and its content
                echo "--- Scan Results ---"
                if [ -s trufflehog.json ]; then
                    ls -lh trufflehog.json
                    cat trufflehog.json
                else
                    echo "No secrets found or file is empty."
                fi

                # 5. Fail the build if TruffleHog found secrets
                if [ $TRUFFLE_EXIT -eq 1 ]; then
                    echo "CRITICAL: Secrets detected in repository!"
                    exit 1
                fi
            '''
        }
    }
    post {
        always {
            // This makes the file downloadable from the Jenkins UI
            archiveArtifacts artifacts: 'trufflehog.json', allowEmptyArchive: true
        }
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
