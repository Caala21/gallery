pipeline {
    agent any 
    environment {
        RENDER_EMAIL = credentials('render-email')
        RENDER_PASSWORD = credentials('render-password')
        RENDER_APP_NAME = 'devops-ip1'
        RENDER_APP_URL = 'https://devops-ip1.onrender.com/'
        SLACK_CHANNEL = 'cliff_ip1'
    }
    tools {
        nodejs 'npm'
    }
    stages {
        stage("Build") {
            steps {
                echo "Cloning code..."
                git branch: 'master', url: 'https://github.com/Caala21/gallery.git'
                echo "Building code..."
                sh 'npm install' 
            }
        }
        stage("Test") {
            steps {
                echo "Testing code..."
                sh 'npm test' 
            }
            post {
                failure {
                    emailext (
                        subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                        body: """<p>FAILURE!</p>
                                <p>Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed.</p>
                                <p>Check console output at '<a href="${env.BUILD_URL}">Build URL</a>'</p>""",
                        to: 'cliff.njogu@student.moringaschool.com',
                        mimeType: 'text/html'
                    )
                }
            }
        }
        stage("Deploy") {
           steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'render-credentials', passwordVariable: 'RENDER_PASSWORD', usernameVariable: 'RENDER_EMAIL')]) {
                        sh "render login --email $RENDER_EMAIL --password $RENDER_PASSWORD"
                        sh "git remote add render git@render.com:${RENDER_APP_NAME}.git"
                        sh "git push render master"
                    }
                }
            }
            post {
                success {
                    script {
                        slackSend (
                            channel: env.SLACK_CHANNEL, 
                            color: '#FFFF00', 
                            message: "Build ${env.BUILD_NUMBER} completed. View the site here: ${RENDER_APP_URL}"
                        )
                    }
                }
            }
        }
    }
}
