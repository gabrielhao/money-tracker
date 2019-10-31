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
                sh 'docker build -t docker-registry.default.svc:5000/money-tracker:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker push docker-registry.default.svc:5000/money-tracker:latest'
            }
        }
    }
}
