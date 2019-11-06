pipeline {
    agent none
    stages {
        stage('Build') {
        agent {
            node {
              label 'dind'
            }
        }
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
        agent {
            node {
              label 'dind'
            }
        }
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Build') {
        agent {
            node {
              label 'dind'
            }
        }
            steps {
                sh 'docker build -t 172.30.1.1:5000/jenkins-test/money-tracker:latest .'
            }
        }
        stage('Docker Push') {
        agent {
            node {
              label 'dind'
            }
        }
            steps {
                sh 'docker login -u developer -p NDWGydCYyjbtyttQ7Mws3_-Yapjsi3o64D55Be81hJc'
                sh 'docker push 172.30.1.1:5000/jenkins-test/money-tracker:latest'
            }
        }
        stage('OpenShift Deploy') {
           agent {
              label 'master'
           }
           steps {
             script {
                openshift.withCluster() {
                   openshift.withProject("money-tracker") {
                   openshift.selector("dc", "money-tracker").rollout().latest()
                   def latestDeploymentVersion = openshift.selector('dc',"money-tracker").object().status.latestVersion
                   def rc = openshift.selector('rc', "money-tracker-${latestDeploymentVersion}")
                   timeout (time: 10, unit: 'MINUTES') {
                     rc.untilEach(1){
                       def rcMap = it.object()
                        return (rcMap.status.replicas.equals(rcMap.status.readyReplicas))
                      }
                   }
                  }
                    echo "completed second rollout"
              }
            }
           }
        }
    }
}
