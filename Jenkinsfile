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
