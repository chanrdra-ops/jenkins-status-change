pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Execute Parallel Selenium Tests') {
            steps {
                sh '/usr/local/Cellar/maven/3.8.4/bin/mvn clean test'
            }
        }

        stage('Update Jira Ticket') {
            steps {
                script {
                    def commitMsg = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
                    echo "Processing commit message: ${commitMsg}"

                    // Safely extract key using a separate non-CPS function
                    def jiraKey = extractJiraKey(commitMsg)

                    if (jiraKey) {
                        echo "Extracted Jira Key: ${jiraKey}"

                        // FIX: Explicitly using site name 'jirauser' matching your configuration
                        jiraAddComment site: 'jirauser', idOrKey: jiraKey, comment: "Automation this one suite ran successfully. Status changed to Done."
                    } else {
                        echo "No valid Jira ticket ID found in this commit message."
                    }
                }
            }
        }
    }
}

// Separate helper function marked with @NonCPS to handle regex safely
@NonCPS
def extractJiraKey(String text) {
    def matcher = (text =~ /[A-Z]+-[0-9]+/)
    if (matcher.find()) {
        return matcher.group(0) // Securely extracts just the plain text string (e.g., SCRUM-1)
    }
    return null
}
