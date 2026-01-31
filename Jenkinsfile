pipeline {
    agent any

    stages {




    stage('Check-Git-Secrets') {
    steps {
        script {
            // We use returnStatus so Jenkins doesn't stop the build instantly
            def exitCode = sh(
                script: '''
                    # 1. Clear old files
                    rm -f trufflehog.json
                    
                    echo "--- Running Scan ---"

                    # 2. Force the --json flag to be the FIRST argument after 'filesystem'
                    # We use /work (the mapped volume) to ensure paths are correct
                    docker run --rm \
                        -v "${WORKSPACE}:/work" \
                        -w /work \
                        ghcr.io/trufflesecurity/trufflehog:latest \
                        filesystem --json --no-update /work > trufflehog.json 2> trufflehog_debug.log || true

                    # 3. Fix permissions immediately
                    chmod 644 trufflehog.json || true
                ''',
                returnStatus: true
            )

            // 4. Inspect the results
            sh '''
                if [ -s trufflehog.json ]; then
                    echo "SUCCESS: JSON report created."
                    # Show the first few findings to the console
                    head -n 20 trufflehog.json
                else
                    echo "ERROR: Report is empty. Printing debug log below:"
                    cat trufflehog_debug.log
                fi
            '''

            // 5. Fail the build if the original process found secrets
            // TruffleHog returns 1 if secrets are found
            if (exitCode == 1) {
                error("Secrets found in repository. Please check trufflehog.json.")
            }
        }
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
