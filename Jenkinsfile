// Standard Declarative Pipeline
pipeline {
    // Defines where the pipeline will execute. 'any' means any available executor.
    agent any 

    tools {
        maven 'Maven 3.9.15' 
    }

    // Global configurations for the entire pipeline
    // options {
    //     timeout(time: 30, unit: 'MINUTES') // Fails the build if it hangs for more than 30 mins
    //     timestamps()                     // Adds time to console output
    //     ansiColor('xterm')               // Enables colorful logs (requires AnsiColor plugin)
    //     disableConcurrentBuilds()        // Prevents multiple builds of the same job from running at once
    // }

    // Parameters allows users to input values when triggering 'Build with Parameters'
    // parameters {
    //     string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Deployment source branch')
    //     booleanParam(name: 'SKIP_TESTS', defaultValue: false, description: 'Check to skip test phase')
    // }

    // Define Environment Variables used across all stages
    // environment {
    //     // We use your Docker container name/alias from docker-compose
    //     DOCKER_IMAGE_NAME = "my-app-frontend"
    //     DOCKER_REGISTRY   = "docker-hub.example.com"
    //     // Example of safe credential handling (Secret Text in Jenkins)
    //     // DEPLOY_TOKEN    = credentials('my-app-deploy-token')
    // }

    stages {
        
        // stage('Checkout') {
        //     steps {
        //         echo "Fetching source code from branch: ${params.BRANCH_NAME}"
        //         // The 'checkout scm' step automatically pulls code from your Git repo
        //         checkout scm
        //     }
        // }

        // stage('Static Analysis') {
        //     steps {
        //         echo "Running Linting and Security Scans..."
        //         // Example: Running a shell command to check code quality
        //         // sh 'npm run lint' or 'mvn checkstyle:check'
        //     }
        // }

        stage('Build step') {
            steps {
                echo "Building app: ${env.BUILD_ID}"
                script {
                    
                    sh "mvn compile"
                }
            }
        }

        stage('Unit tests step') {
            // Conditional execution: skip if user checked SKIP_TESTS parameter
            when {
                expression { params.SKIP_TESTS == false }
            }
            steps {
                echo "Executing unit tests..."
                sh "mvn clean test"
            }
        }

        stage('Package step') {

            when { branch 'main' }
            steps {
                echo "Packaging app..."
                sh "mvn package -DskipTests"
            }
        }

        // stage('Deploy') {
        //     steps {
        //         echo "Starting deployment process..."
        //         // Example: trigger a script or call an API to restart services
        //         // sh './deploy.sh'
        //     }
        // }
    }

    // Post-build actions based on the result of the pipeline
    post {
        always {
            echo "Pipeline finished..."
        }
        success {
            echo "Build Success!"
        }
        failure {
            echo "Build Failed! Check BlueOcean logs for details."
        }
        aborted {
            echo "Build was cancelled."
        }
    }
}
