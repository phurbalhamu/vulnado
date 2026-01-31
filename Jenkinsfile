pipeline {
    agent any

    stages {




        stage('Check-Git-Secrets') {
    steps {
        sh '''
            # 1. Create the file first and give it full permissions 
            # This prevents Docker 'root' vs Jenkins 'user' permission issues
            touch trufflehog.json
            chmod 666 trufflehog.json

            echo "--- Starting Scan ---"

            # 2. Run with --json flag. 
            # Note: The "piggy" emojis will still show in the Jenkins console (stderr), 
            # but the raw data will go into the file (stdout).
            docker run --rm \
                -v "${WORKSPACE}:/work" \
                -w /work \
                ghcr.io/trufflesecurity/trufflehog:latest \
                filesystem /work \
                --json \
                --no-update > trufflehog.json

            # 3. Check if the file actually has data now
            if [ -s trufflehog.json ]; then
                echo "SUCCESS: JSON report generated."
                ls -lh trufflehog.json
                # Optional: print the first few lines to the console to be sure
                head -n 5 trufflehog.json
            else
                echo "ERROR: Report is still empty. Checking permissions..."
                ls -la trufflehog.json
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
