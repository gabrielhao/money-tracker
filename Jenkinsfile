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
                sh 'docker build -t 172.30.1.1:5000/money-tracker/money-tracker:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'cat /run/secrets/kubernetes.io/serviceaccount/token | docker login -u developer --password-stdin 172.30.1.1:5000'
                sh 'docker push 172.30.1.1:5000/money-tracker/money-tracker:latest'
            }
        }
        stage('OpenShift Deploy') {
           steps {
             sh 'oc project money-tracker'
             sh 'oc rollout latest dc/money-tracker'
           }
        }
    }
}
