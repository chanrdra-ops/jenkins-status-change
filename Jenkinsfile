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
                // We use the absolute path right here so Mac absolutely cannot say "command not found"
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

                        // Transitions issue to Done using ID 41
                        jiraTransitionIssue idOrKey: jiraKey, input: [transition: [id: '41']]
                        jiraAddComment comment: "Automation suite ran successfully. Status changed to Done.", idOrKey: jiraKey
                    } else {
                        echo "No valid Jira ticket ID found in commit message."
                    }
                }
            }
        }
    }
}