pipeline {
    agent any

    stages {

        // 1. docker / docker-compose 실행 가능 여부 확인 (없으면 설치)
        stage('Docker & Docker Compose 준비') {
            steps {
                sh '''
                    set -e
                    echo ">>> Docker 버전 확인"
                    docker --version

                    if docker compose version >/dev/null 2>&1; then
                        echo ">>> docker compose(V2 plugin) 사용 가능"
                        echo "docker compose" > .compose_cmd
                    elif command -v docker-compose >/dev/null 2>&1; then
                        echo ">>> docker-compose(V1) 사용 가능"
                        echo "docker-compose" > .compose_cmd
                    else
                        echo ">>> docker compose 를 찾을 수 없어 설치를 진행합니다"
                        sudo apt-get update
                        sudo apt-get install -y docker-compose-plugin
                        echo "docker compose" > .compose_cmd
                    fi

                    echo ">>> 사용할 compose 명령어: $(cat .compose_cmd)"
                '''
            }
        }

        // 2. n8n 폴더로 들어가서 docker-compose 실행 후 빠져나오기
        stage('n8n 배포') {
            steps {
                dir('n8n') {
                    sh '''
                        set -e
                        COMPOSE_CMD=$(cat ../.compose_cmd)
                        echo ">>> [n8n] $COMPOSE_CMD up -d 실행"
                        $COMPOSE_CMD up -d
                    '''
                }
                // dir 블록을 벗어나면 자동으로 project 루트로 돌아옵니다
            }
        }

        // 3. project 루트의 docker-compose 실행 → 실제 배포
        stage('Project 배포') {
            steps {
                sh '''
                    set -e
                    COMPOSE_CMD=$(cat .compose_cmd)
                    echo ">>> [project] $COMPOSE_CMD up -d --build 실행"
                    $COMPOSE_CMD up -d --build
                '''
            }
        }
    }

    post {
        success {
            echo '✅ 배포가 완료되었습니다.'
        }
        failure {
            echo '❌ 배포에 실패했습니다. 콘솔 로그를 확인하세요.'
        }
        always {
            sh 'rm -f .compose_cmd'
        }
    }
}
