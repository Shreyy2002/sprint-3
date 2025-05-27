# Declarative Jenkins Pipeline | Notification

---

## Intro 
A Declarative Pipeline in Jenkins is a way of defining and managing Continuous Integration/Continuous Delivery (CI/CD) workflows using a simplified, structured syntax
Declarative Pipelines are designed to be more readable and maintainable, promoting consistent and concise pipeline definition.

---

## Prerequisite
- Install slack and email template plugin.
- Setup slack and email global configurations.

---

## Declarative Pipeline for Notification

```groovy
pipeline {
    agent any

    environment {
        NOTIFY_EMAIL = 'shreytyagi75@gmail.com'
        SLACK_CHANNEL = '#slack-notification' 
    }

    options {
   
        timestamps()
    }

    stages {
        stage('Test Success Notification') {
            steps {
                echo 'Running a SUCCESS simulation...'
                sh 'exit 0'
            }
        }

        stage('Test Failure Notification') {
            steps {
                echo 'Running a FAILURE simulation (intentionally fails)...'
                // This command will fail and trigger the 'failure' block
                sh 'exit 1'
            }
        }
    }

    post {
        success {
            echo '✅ Build Succeeded!'
            slackSend (
                channel: "${env.SLACK_CHANNEL}",
                color: '#00FF00',
                message: "✅ SUCCESS: *${env.JOB_NAME}* #${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
            )
            mail to: "${env.NOTIFY_EMAIL}",
                 subject: "✅ Jenkins Job Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: """\
The Jenkins job *${env.JOB_NAME}* completed successfully.

Details:
${env.BUILD_URL}
"""
        }

        failure {
            echo '❌ Build Failed!'
            slackSend (
                channel: "${env.SLACK_CHANNEL}",
                color: '#FF0000',
                message: "❌ FAILURE: *${env.JOB_NAME}* #${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
            )
            mail to: "${env.NOTIFY_EMAIL}",
                 subject: "❌ Jenkins Job Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: """\
The Jenkins job *${env.JOB_NAME}* has failed.

Logs:
${env.BUILD_URL}console
"""
        }

        always {
            echo "📣 Notification testing complete. Build result: ${currentBuild.currentResult}"
        }
    }
}
```

- Above is the pipeline code for the declarative pipeline, which sends the notification to slack and email about the build whether it fails or success.
- Below are the screenshots of notification recieved on email and slack.
  
![Screenshot 2025-05-26 195355](https://github.com/user-attachments/assets/2cca0160-1b44-4463-b8c5-c0f6af39a8cf)
![Screenshot 2025-05-26 195438](https://github.com/user-attachments/assets/1f08a7a0-145d-4330-9fc2-8941ec223e8a)

