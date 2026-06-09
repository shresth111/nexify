pipeline {
    agent any

    parameters {
        string(name: 'EC2_HOST', defaultValue: '13.233.104.170', description: 'EC2 public IP / DNS')
        string(name: 'EC2_USER', defaultValue: 'ubuntu',        description: 'SSH user on EC2')
        string(name: 'DEPLOY_DIR', defaultValue: '/home/ubuntu/nexify', description: 'App directory on EC2')
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/shresth111/nexify.git', description: 'Repo to clone on EC2 (first run only)')
        string(name: 'GIT_BRANCH', defaultValue: 'main', description: 'Branch to deploy')
    }

    environment {
        // Jenkins credential ID for the EC2 .pem private key (type: "SSH Username with private key")
        EC2_SSH_CRED = 'nexify-ec2-key'
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

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ["${EC2_SSH_CRED}"]) {
                    // ${VAR} are expanded by the Jenkins shell (params/env) before being sent over SSH.
                    // The closing EOF MUST stay flush-left.
                    sh '''
ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} bash -s <<EOF
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
echo "Nexify Webgrip deployed successfully!"
EOF
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for site to come up..."
                    sleep 5
                    for i in 1 2 3 4 5; do
                        code=$(curl -s -o /dev/null -w "%{http_code}" http://${EC2_HOST} || echo 000)
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
            echo "Deploy complete -> http://${params.EC2_HOST}"
        }
        failure {
            echo "Build/Deploy failed. Check the stage logs above."
        }
    }
}
