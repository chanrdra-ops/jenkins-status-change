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

                    def jiraKey = extractJiraKey(commitMsg)

                    if (jiraKey) {
                        echo "Extracted Jira Key: ${jiraKey}"

                        // Post comment
                        jiraAddComment site: 'jirauser', idOrKey: jiraKey, comment: "Automation suite executed successfully."

                        // FIX: Transition the issue status directly.
                        // Try transitioning by the destination name 'Done' first.
                        jiraTransitionIssue site: 'jirauser', idOrKey: jiraKey, id: '31' // Replace '31' with your workflow's unique transition ID
                    } else {
                        echo "No valid Jira ticket ID found in this commit message."
                    }
                }
            }
        }
    }
}

@NonCPS
def extractJiraKey(String text) {
    def matcher = (text =~ /[A-Z]+-[0-9]+/)
    if (matcher.find()) {
        return matcher.group(0)
    }
    return null
}
