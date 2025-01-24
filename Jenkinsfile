pipeline {
    agent { 
        label 'maven' // Use your Maven server as the agent
    }

    environment {
        GITHUB_TOKEN = credentials('github-token-id') // GitHub credentials
    }

    stages {
        stage('Git Checkout') {
            steps {
                // Checkout code from the correct branch
                git url: 'https://github.com/vickeyys/jenkins-java-project-1.git', credentialsId: 'github-token-id'
            }
        }

        stage('Compile') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Compiling for master (Production)"
                        // Compile with production profile
                        sh 'mvn clean compile -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Compiling for Staging"
                        // Compile with staging profile
                        sh 'mvn clean compile -P stag'
                    } else {
                        echo "No valid environment for branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Running tests for master (Production)"
                        // Test with production profile
                        sh 'mvn test -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Running tests for Staging"
                        // Test with staging profile
                        sh 'mvn test -P stag'
                    } else {
                        echo "No valid environment for branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Building for master (Production)"
                        // Build with production profile
                        sh 'mvn package -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Building for Staging"
                        // Build with staging profile
                        sh 'mvn package -P stag'
                    } else {
                        echo "No valid environment for branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        echo "Deploying to Production Nexus Repository"
                        // Deploy to production repository
                        sh 'mvn deploy -P prod'
                    } else if (env.BRANCH_NAME == 'stag') {
                        echo "Deploying to Staging Nexus Repository"
                        // Deploy to staging repository
                        sh 'mvn deploy -P stag'
                    } else {
                        echo "Branch ${env.BRANCH_NAME} is not recognized for deployment"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully for branch: ${env.BRANCH_NAME}"
        }
        failure {
            echo "Pipeline failed for branch: ${env.BRANCH_NAME}"
        }
    }
}
