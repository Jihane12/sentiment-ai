// Jenkinsfile - Pipeline CI/CD SentimentAI
pipeline {
    agent any
    environment {
        IMAGE_NAME = 'sentiment-ai'
        REGISTRY = 'ghcr.io/jihane12'
        IMAGE_TAG = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
    }
    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo "Branche : ${env.BRANCH_NAME}"
                echo "Commit : ${env.GIT_COMMIT}"
                sh 'git log --oneline -5'
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    docker run --rm \
                    --volumes-from jenkins \
                    -w $WORKSPACE \
                    python:3.12-slim \
                    sh -c "pip install flake8 -q && flake8 src/ --max-line-length=100"
                '''
            }
        }

        stage('Build & Test') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .

                docker rm -f test-runner 2>/dev/null || true

                set +e
                docker run \
                  -e CI=true \
                  --name test-runner \
                  ${IMAGE_NAME}:${IMAGE_TAG} \
                  pytest tests/ -v \
                    --cov=src \
                    --cov-report=xml:/tmp/coverage.xml \
                    --cov-report=term-missing \
                    --cov-fail-under=70
                TEST_EXIT_CODE=$?
                set -e
                exit $TEST_EXIT_CODE
                '''
            }
        }

            }
            steps {
                      --network cicd-network \
                      --volumes-from jenkins \
                      -w "$WORKSPACE" \
                      -e SONAR_HOST_URL="$SONAR_HOST_URL" \
                      -e SONAR_TOKEN="$SONARQUBE_TOKEN" \
                      sonarsource/sonar-scanner-cli:latest \
                      sonar-scanner \
                        -Dsonar.projectKey=sentiment-ai \
                        -Dsonar.projectName=SentimentAI \
                        -Dsonar.projectBaseDir="$WORKSPACE" \
                        -Dsonar.sources=src \
                        -Dsonar.python.version=3.11 \                withSonarQubeEnv('sonarqube') {
                    sh '''
                    docker run --rm \
        stage('SonarQube Analysis') {
            environment {
                SONARQUBE_TOKEN = credentials('sonar-token')
            post {
                failure { echo 'Tests échoués ou coverage insuffisant (<70%)' }
            }

                docker cp test-runner:/tmp/coverage.xml ./coverage.xml 2>/dev/null || true
                docker rm -f test-runner 2>/dev/null || true
