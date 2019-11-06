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
                sh 'cat /run/secrets/kubernetes.io/serviceaccount/token | docker login -u developer --password-stdin docker-registry.default.svc.cluster.local:5000'
                sh 'docker push 172.30.1.1:5000/jenkins-test/money-tracker:latest'
            }
        }
        stage('OpenShift Deploy') {
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
