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
                // Using absolute path ensures Jenkins finds your local Mac Maven installation
                sh '/usr/local/Cellar/maven/3.8.4/bin/mvn clean test'
            }
        }

        stage('Update Jira Ticket') {
            steps {
                script {
                    def commitMsg = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
                    echo "Processing commit message: ${commitMsg}"

                    def matcher = (commitMsg =~ /[A-Z]+-[0-9]+/)

                    if (matcher.find()) {
                        def jiraKey = matcher[0]
                        echo "Extracted Jira Key: ${jiraKey}"

                        // Correct plugin syntax to transition the issue using transition ID '41'
                        jiraTransitionResult transitionId: '41', idOrKey: jiraKey

                        // Adds the comment confirming successful pipeline run
                        jiraAddComment comment: "Automation suite ran successfully. Status changed to Done.", idOrKey: jiraKey
                    } else {
                        echo "No valid Jira ticket ID found in commit message."
                    }
                }
            }
        }
    }
}