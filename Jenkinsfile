pipeline {
    agent any
    parameters {
        string(name: 'DEPLOY_DIR', defaultValue: '/home/ubuntu/nexify', description: 'App directory')
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/shresth111/nexify.git', description: 'Repo to clone (first run only)')
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Branch to deploy')
        string(name: 'HEALTH_HOST', defaultValue: 'localhost', description: 'Host for health check')
    }
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
                    if [ ! -d "${DEPLOY_DIR}/.git" ]; then
                        echo "First deploy — cloning repo..."
                        git clone ${GIT_REPO} ${DEPLOY_DIR}
                    fi
                    cd ${DEPLOY_DIR}
                    git fetch origin ${GIT_BRANCH}
                    git reset --hard origin/${GIT_BRANCH}
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
                        code=$(curl -s -o /dev/null -w "%{http_code}" http://${HEALTH_HOST} || echo 000)
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
            echo "Deploy complete -> http://${params.HEALTH_HOST}"
        }
        failure {
            echo "Build/Deploy failed. Check the stage logs above."
        }
    }
}
