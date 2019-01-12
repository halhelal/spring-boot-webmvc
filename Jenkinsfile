pipeline {
    agent any
    stages {
        stage('preamble') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Using project: ${openshift.project()}"
                        }
                    }
                }
            }
        }
        stage('build') {
            steps {
                sh "mvn clean package"
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            def builds = openshift.selector("bc", "spring-boot-webmvc").related('builds')
                            timeout(5) {
                                builds.untilEach(1) {
                                    return (it.object().status.phase == "Complete")
                                }
                            }
                        }
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            def rm = openshift.selector("dc", templateName).rollout()
                            timeout(5) {
                                openshift.selector("dc", "spring-boot-webmvc").related('pods').untilEach(1) {
                                    return (it.object().status.phase == "Running")
                                }
                            }
                        }
                    }
                }
            }
        }
        stage('tag') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            openshift.tag("spring-boot-webmvc:latest", "spring-boot-webmvc-staging:latest")
                        }
                    }
                }
            }
        }
    }
}