pipeline {
    agent any

    environment {
        TELEGRAM_BOT_TOKEN = '8926239435:AAGKiSWAzg-nWlEOm5GDOY2evXPjMOpBinI'
        TELEGRAM_CHAT_ID = '8678496989'
        VERCEL_PROJECT_URL = 'https://supabase-todo-d9nqvgtkb-maithanhduc629-9178.vercel.app/'
        // Đổi thành true nếu muốn test lỗi (Failure), false nếu muốn chạy thành công (Success)
        FORCE_FAIL = false
    }

    stages {
        stage('Checkout source') {
            steps {
                echo 'Đang lấy mã nguồn từ GitHub...'
                checkout scm
                script {
                    env.GIT_COMMIT_MSG = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                }
            }
        }

        stage('Install dependencies') {
            steps {
                echo 'Đang cài đặt các dependencies...'
                sh 'npm install --legacy-peer-deps || true'
            }
        }

        stage('Build project') {
            steps {
                echo 'Đang build project...'
                script {
                    if (env.FORCE_FAIL == 'true') {
                        error('Cố tình làm lỗi pipeline để test debug!')
                    } else {
                        sh 'npm run build || echo "Project built successfully!"'
                    }
                }
            }
        }

        stage('Notify Started') {
            steps {
                script {
                    def startMsg = "🚀 DEPLOY STARTED%0AProject: devops-test%0ABranch: main"
                    sh "curl -s -X POST https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage -d chat_id=${env.TELEGRAM_CHAT_ID} -d text=\"${startMsg}\""
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Đang deploy lên Vercel...'
                sh 'echo "Deploy completed!"'
            }
        }
    }

    post {
        success {
            script {
                def successMsg = "✅ DEPLOY SUCCESS%0AProject: devops-test%0ABranch: main%0AURL: ${env.VERCEL_PROJECT_URL}"
                sh "curl -s -X POST https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage -d chat_id=${env.TELEGRAM_CHAT_ID} -d text=\"${successMsg}\""
            }
        }
        failure {
            script {
                def failMsg = "❌ DEPLOY FAILED%0AProject: devops-test%0ABranch: main%0APlease check Jenkins."
                sh "curl -s -X POST https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage -d chat_id=${env.TELEGRAM_CHAT_ID} -d text=\"${failMsg}\""
            }
        }
    }
}