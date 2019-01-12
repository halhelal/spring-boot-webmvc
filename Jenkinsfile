pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.startBuild("spring-boot-webmvc-s2i", "--from-file=build/spring-boot-webmvc-1.0-SNAPSHOT.jar", "--wait")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    openshift.withCluster() {
                        def dc = openshift.selector("dc", "spring-boot-webmvc")
                        dc.rollout().latest()
                        dc.rollout().status()
                    }
                }
            }
        }
    }
}