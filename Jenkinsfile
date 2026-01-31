pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Secret Scan (TruffleHog)') {
            steps {
                script {
                    // 1. Run TruffleHog with JSON output and exclude compiled JARs
                    // 2. We use '|| true' because TruffleHog returns 1 if secrets are found, 
                    //    which would stop the build before we can archive the results.
                    sh '''
                        trufflehog filesystem --json --no-update --no-verification --exclude-paths=.gitignore . > trufflehog.json || true
                    '''
                }
            }
        }

        stage('Dependency Scan (OWASP)') {
            steps {
                // This assumes you have the 'dependency-check' tool installed in your container or environment
                // It generates ALL formats (XML, HTML, JSON) as seen in your logs
                sh 'dependency-check --project "MyProject" --scan . --format ALL --out .'
            }
        }
    }

    post {
        always {
            // --- 1. ARCHIVE ALL FILES ---
            // This puts the raw files in the "Build Artifacts" section for download
            archiveArtifacts artifacts: 'trufflehog.json, dependency-check-report.*', allowEmptyArchive: true

            // --- 2. NATIVE DASHBOARD ---
            // This creates the "Dependency-Check" interactive link on the left sidebar
            dependencyCheckPublisher pattern: 'dependency-check-report.xml'

            // --- 3. HTML REPORT VIEW ---
            // This embeds the HTML report directly into the Jenkins UI
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: '.',
                reportFiles: 'dependency-check-report.html',
                reportName: 'Dependency Analysis (HTML)'
            ])
            
            // --- 4. TRUFFLEHOG CONSOLE SUMMARY ---
            script {
                if (fileExists('trufflehog.json')) {
                    echo "--- TruffleHog JSON Findings (Preview) ---"
                    sh "head -n 10 trufflehog.json"
                }
            }
        }
        
        fixed {
            echo "Security issues resolved!"
        }
    }
}
