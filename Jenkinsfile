pipeline {
    agent any

    triggers {
        // Keeps the hook trigger active via code definition
        githubPush()
    }

    stages {
        stage('Checkout Code') {
            steps {
                // This downloads the specific commit version from GitHub dynamically
                checkout scm
            }
        }

        stage('Execute Parallel Selenium Tests') {
            steps {
                // Runs your multi-threaded Maven configuration on your local machine
                sh 'mvn clean test'
            }
        }

        stage('Update Jira Ticket') {
            steps {
                script {
                    // Extract Jira issue key (e.g., SCRUM-1) from the latest commit message
                    def commitMsg = sh(script: "git log -1 --pretty=%B", returnStdout: true).trim()
                    echo "Processing commit message: ${commitMsg}"

                    def matcher = (commitMsg =~ /[A-Z]+-[0-9]+/)

                    if (matcher.find()) {
                        def jiraKey = matcher[0]
                        echo "Extracted Jira Key: ${jiraKey}"

                        // Moves the matching ticket to the "Done" column using ID 41
                        jiraTransitionIssue idOrKey: jiraKey, input: [transition: [id: '41']]
                        jiraAddComment comment: "Parallel automation execution completed successfully via local Jenkins pipeline.", idOrKey: jiraKey
                    } else {
                        echo "No valid script uppercase Jira ticket ID found in this commit message. Skipping transition."
                    }
                }
            }
        }
    }
}