pipeline {

    /*********************************************************
     * AGENT SECTION
     * -------------------------------------------------------
     * "agent any" allows Jenkins to run the pipeline on any
     * available executor node. This means the entire pipeline
     * will run on the Jenkins server (your EC2 instance).
     *********************************************************/
    agent any

    stages {

        /*********************************************************
         * STAGE 1: Clone Repository
         * -------------------------------------------------------
         * Jenkins pulls your project files from GitHub into the
         * workspace. This ensures it always works with the latest
         * code for repeatable builds.
         *********************************************************/
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: [[url: 'https://github.com/FIDMANN-TECH/VERSION-CONTROL-MINIPROJECT.git']]
            }
        }


        /*********************************************************
         * STAGE 2: Build Docker Image
         * -------------------------------------------------------
         * Jenkins builds a Docker image using the Dockerfile.
         * "-t my-pipeline-app" gives the image a tag (name).
         *********************************************************/
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-pipeline-app .'
            }
        }


        /*********************************************************
         * STAGE 3: Remove Old Container
         * -------------------------------------------------------
         * If a container with the same name exists, Jenkins removes
         * it to avoid errors like "port already in use".
         *********************************************************/
        stage('Remove Old Container') {
            steps {
                sh '''
                if [ $(docker ps -aq --filter name=my-pipeline-container) ]; then
                    docker stop my-pipeline-container
                    docker rm my-pipeline-container
                fi
                '''
            }
        }


        /*********************************************************
         * STAGE 4: Run New Container on Port 8081
         * -------------------------------------------------------
         * Jenkins runs the Docker container.
         *
         * Port Mapping:
         *   EC2    → Container
         *   8081   → 80
         *
         * This makes the website accessible at:
         *   http://EC2_PUBLIC_IP:8081
         *********************************************************/
        stage('Run Docker Container') {
            steps {
                sh 'docker run -d --name my-pipeline-container -p 8081:80 my-pipeline-app'
            }
        }


        /*********************************************************
         * STAGE 5: Verify Running Container
         * -------------------------------------------------------
         * Displays running Docker containers. Useful for logs and
         * screenshot documentation.
         *********************************************************/
        stage('Verify Container Status') {
            steps {
                sh 'docker ps -a'
            }
        }
    }
}
