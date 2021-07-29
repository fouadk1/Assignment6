def commit_id 
pipeline {
    agent any
    stages {
        stage('preparation') {
            steps {
                checkout scm 
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('build') {
            steps {
                echo 'building maven workload'
                sh "mvn clean install"
                echo "build complete"
            }
        }
       stage('Image Build') {
            steps {
                echo 'Building....'
                sh "minikube ssh 'docker build -t position-simulator:${commit_id} ./'"
                echo 'build complete'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|position-simulator:${commit_id}|' workloads.yaml"
                sh 'kubectl apply -f workloads.yaml'
            }
        }
    }
} 


