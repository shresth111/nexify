pipeline {
    agent any
    options {
        timestamps()
        disableConcurrentBuilds()
        timeout(time: 15, unit: 'MINUTES')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'echo "Building commit: $(git rev-parse --short HEAD)"'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    set -e
                    docker compose down || true
                    docker compose up -d --build
                    docker image prune -f || true
                    echo "Nexify deployed successfully!"
                '''
            }
        }
        stage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for site to come up..."
                    sleep 5
                    for i in 1 2 3 4 5; do
                        code=$(curl -s -o /dev/null -w "%{http_code}" http://localhost || echo 000)
                        if [ "$code" = "200" ]; then
                            echo "Site is live (HTTP $code)"
                            exit 0
                        fi
                        echo "Attempt $i: got HTTP $code, retrying..."
                        sleep 5
                    done
                    echo "Health check failed."
                    exit 1
                '''
            }
        }
    }
    post {
        success {
            echo "Deploy complete -> http://localhost"
        }
        failure {
            echo "Build/Deploy failed. Check the stage logs above."
        }
    }
}
