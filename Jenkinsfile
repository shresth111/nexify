stage('Deploy') {
    steps {
        sh """
            set -e
            DEPLOY_DIR='${params.DEPLOY_DIR}'
            GIT_REPO='${params.GIT_REPO}'
            GIT_BRANCH='${params.GIT_BRANCH}'

            : "\${DEPLOY_DIR:?DEPLOY_DIR is empty}"
            : "\${GIT_REPO:?GIT_REPO is empty}"
            : "\${GIT_BRANCH:?GIT_BRANCH is empty}"

            if [ ! -d "\$DEPLOY_DIR/.git" ]; then
                echo "First deploy — cloning repo..."
                git clone "\$GIT_REPO" "\$DEPLOY_DIR"
            fi
            cd "\$DEPLOY_DIR"
            git fetch origin "\$GIT_BRANCH"
            git reset --hard "origin/\$GIT_BRANCH"
            docker compose down || true
            docker compose up -d --build
            docker image prune -f || true
            echo "Nexify deployed successfully!"
        """
    }
}
