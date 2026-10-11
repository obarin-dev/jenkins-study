// Jenkins 学習用パイプライン
// このファイルを Jenkins が Git から読み込んで実行する
pipeline {
    agent any

    stages {
        stage('コード確認') {
            steps {
                echo '=== リポジトリの中身 ==='
                sh 'ls -la'
                sh 'git log --oneline -1'
            }
        }

        stage('AIレビュー') {
            steps {
                echo '=== AIレビュー ==='
                sh '''
                    set -e

                    # 1. 直近コミットの差分を取り出す（これがレビュー対象）
                    git diff HEAD~1 HEAD > review.diff || true
                    LINES=$(wc -l < review.diff | tr -d " ")
                    echo "レビュー対象: ${LINES} 行の差分"

                    if [ "${LINES}" -eq 0 ]; then
                        echo "変更なし。レビューをスキップします"
                        echo "変更なし" > review-result.md
                        exit 0
                    fi

                    echo "--- 差分（先頭20行） ---"
                    head -20 review.diff

                    # 2. 本来はここで Claude に渡す
                    #    claude -p "あなたはコードレビュアーです。以下の差分をレビューし、
                    #               N+1クエリ・セキュリティ・命名の観点で指摘してください。" \
                    #           < review.diff > review-result.md
                    #
                    # APIキー未設定のため、今はダミー結果を生成する
                    {
                      echo "# AIレビュー結果（ダミー）"
                      echo ""
                      echo "- 対象: ${LINES} 行の差分"
                      echo "- 状態: ANTHROPIC_API_KEY 未設定のため未実行"
                      echo ""
                      echo "## 有効化の手順"
                      echo "1. console.anthropic.com でAPIキーを発行"
                      echo "2. Jenkins の Credentials に Secret text として登録"
                      echo "3. Jenkinsfile の claude -p 行のコメントを外す"
                    } > review-result.md

                    echo "--- レビュー結果 ---"
                    cat review-result.md
                '''
                archiveArtifacts artifacts: 'review.diff,review-result.md', allowEmptyArchive: true
            }
        }

        stage('準備') {
            steps {
                echo '=== 準備中 ==='
                sh 'mkdir -p build'
                sh 'echo "設定ファイル" > build/config.txt'
            }
        }

        stage('テスト') {
            steps {
                echo '=== テスト中 ==='
                sh 'sleep 2'
                sh 'echo "テスト 1 ... OK"'
                sh 'echo "テスト 2 ... OK"'
                sh 'echo "テスト 3 ... OK （自動実行の確認用）"'
            }
        }

        stage('ビルド') {
            steps {
                echo '=== ビルド中 ==='
                sh 'sleep 2'
                sh 'echo "成果物" > build/app.txt'
            }
        }

        stage('デプロイ') {
            steps {
                echo '=== デプロイ中 ==='
                sh 'cat build/app.txt'
            }
        }
    }

    post {
        success {
            echo '✅ 全部成功しました（ここにSlack通知を書く）'
        }
        failure {
            echo '❌ 失敗しました（ここに通知やIssue作成を書く）'
        }
        always {
            echo '📋 成功しても失敗しても必ず実行される（後片付けなど）'
        }
    }
}
