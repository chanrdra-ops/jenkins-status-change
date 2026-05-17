pipeline {
    agent any

    tools {
        // This injects the Maven paths we just saved in the Jenkins UI
        maven 'Maven3'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Execute Parallel Selenium Tests') {
            steps {
                // Jenkins will now know exactly what 'mvn' means!
                sh 'mvn clean test'
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

                        // Moves your card to Done (ID 41)
                        jiraTransitionIssue idOrKey: jiraKey, input: [transition: [id: '41']]
                        jiraAddComment comment: "Automation run successful. Status transitioned dynamically via local Jenkins.", idOrKey: jiraKey
                    } else {
                        echo "No changes  made valid Jira ticket ID found in commit message."
                    }
                }
            }
        }
    }
}