pipeline {
    agent any

    stages {
        stage('Pull Code') {
            steps {
                // No repo URL needed here! Jenkins gets it from the UI configuration.
                checkout scm
            }
        }

        stage('Execute Parallel Selenium Tests') {
            steps {
                // This triggers your Maven parallel settings on your local machine
                sh 'mvn clean test'
            }
        }

        stage('Update Jira to Done') {
            steps {
                script {
                    // Extract Jira Key (e.g., SCRUM-1) from the latest commit message
                    def commitMsg = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
                    def jiraKey = (commitMsg =~ /[A-Z]+-[0-9]+/)[0]

                    if (jiraKey) {
                        echo "Found Jira Ticket: ${jiraKey}. Updating status..."
                        // Uses Transition ID 41 to move your ticket to Done
                        jiraTransitionIssue idOrKey: jiraKey, input: [transition: [id: '41']]
                    } else {
                        echo "No Jira ticket key found in commit message. Skipping update."
                         echo "No Jira ticket key found in commit message. Skipping update."
                    }
                }
            }
        }
    }
}