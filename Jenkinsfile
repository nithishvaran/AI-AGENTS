pipeline {
    agent any

    stages {
        stage('Run Python') {
            steps {
                sh '''
                    set +e
                    python3 hello.py > build.log 2>&1
                    rc=$?
                    cat build.log
                    exit $rc
                '''
            }
        }
    }
    post {
        failure {
            sh '''
                set -eu
                python3 -m venv .venv-ai-agent
                .venv-ai-agent/bin/python -m pip install --quiet -r ai-agents/requirements.txt
            '''
            withCredentials([
                string(credentialsId: 'claude-api-key', variable: 'ANTHROPIC_API_KEY'),
                usernamePassword(credentialsId: 'gmail-app-password', usernameVariable: 'GMAIL_USER', passwordVariable: 'GMAIL_APP_PASSWORD'),
                string(credentialsId: 'failure-email-to', variable: 'FAILURE_EMAIL_TO')
            ]) {
                sh '.venv-ai-agent/bin/python ai-agents/analyze_failure.py'
            }
        }
    }
}
