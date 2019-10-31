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
                sh 'docker build -t 172.30.1.1:5000/jenkins-test/money-tracker:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker push 172.30.1.1:5000/jenkins-test/money-tracker:latest'
            }
        }
    }
}
