pipeline {
    agent any

    stages {




    stage('Check-Git-Secrets') {
    steps {
        sh '''
            # 1. Clear previous runs
            rm -f trufflehog.json

            echo "--- Executing TruffleHog Scan ---"

            # 2. Run Docker with absolute workspace path and internal redirection
            # We use --json to enable the data stream
            # We use --fail to ensure Jenkins fails IF secrets are found
            docker run --rm \
                -v "${WORKSPACE}:/work" \
                -w /work \
                ghcr.io/trufflesecurity/trufflehog:latest \
                filesystem . \
                --json \
                --no-update > trufflehog.json || TRUFFLE_EXIT=$?

            # 3. Handle permissions (Docker creates files as root)
            sudo chmod 644 trufflehog.json || true

            # 4. Verification check
            if [ -s trufflehog.json ]; then
                echo "SUCCESS: Found $(grep -c "SourceID" trufflehog.json) potential secrets."
                # Display JSON for the logs
                cat trufflehog.json
            else
                echo "WARNING: No JSON findings were written to the file."
                # If the file is empty, it means no secrets were detected
            fi

            # 5. Respect the exit code if secrets were found
            if [ "$TRUFFLE_EXIT" == "1" ]; then
                echo "Build failing due to secrets found."
                exit 1
            fi
        '''
    }
    post {
        always {
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
