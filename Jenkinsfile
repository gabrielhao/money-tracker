pipeline {
    agent {
        node {
          label 'dind'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Build') {
            steps {
                docker build . -t image-registry.openshift-image-registry.svc:5000/money-tracker:latest
            }
        }
    }
}
