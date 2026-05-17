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

                        // Jenkins sends the comment. Your Jira flow will intercept this comment
                        // and handle shifting the ticket status on its side automatically.
                        jiraComment issueKey: jiraKey, body: "Automation this one suite ran successfully. Status changed to Done."
                    } else {
                        echo "No valid  Jira ticket ID found in NewChanges this one message."
                    }
                }
            }
        }
    }
}