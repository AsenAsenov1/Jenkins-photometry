pipeline {
    agent none  // No default agent for the whole pipeline

    environment {
        images_dir = 'pipeline-images'
    }

    stages {
        stage('Prepare') {
            agent {
                kubernetes {
                    label 'photometry-agent-nfs'
                    defaultContainer 'shell'
                    retries 2
                }
            }
            steps {
                container('shell') {
                    //sh "sleep 600"
                    sh """
                        pwd
                        cd ${env.images_dir}
                        pwd
                        ls *.fits > list && split -n 2 list agent_ && rm list
                       """
                }
            }
        }

        stage('Register') {
            parallel {
                stage('Register - Agent 1') {
                    agent {
                        kubernetes {
                            label 'photometry-agent-nfs'
                            defaultContainer 'shell'
                            retries 2
                            nodeSelector 'kubernetes.io/hostname=worker-01'
                        }
                    }
                    steps {
                        container('shell') {
                            sh """
                                 cd ${env.images_dir}
                                 cat agent_aa
                               """
                        }
                    }
                }
                stage('Register - Agent 2') {
                    agent {
                        kubernetes {
                            label 'photometry-agent-nfs'
                            defaultContainer 'shell'
                            retries 2
                            nodeSelector 'kubernetes.io/hostname=worker-02'
                        }
                    }
                    steps {
                        container('shell') {
                            sh """
                               cd ${env.images_dir}
                               cat agent_ab
                               """
                        }
                    }
                }
            }
        }

        stage('Photometry') {
            agent {
                kubernetes {
                    label 'photometry-agent-nfs'
                    defaultContainer 'shell'
                    retries 2
                }
            }
            steps {
                container('shell') {
                    sh """
                       cd ${env.images_dir}
                       exit 1
                       """
                }
            }
        }

        stage('Calibrate') {
            agent {
                kubernetes {
                    label 'photometry-agent-nfs'
                    defaultContainer 'shell'
                    retries 2
                }
            }
            steps {
                container('shell') {
                    sh """
                       cd ${env.images_dir}
                       ls -l *.fits
                       """
                }
            }
        }

        stage('Distill') {
            agent {
                kubernetes {
                    label 'photometry-agent-nfs'
                    defaultContainer 'shell'
                    retries 2
                }
            }
            steps {
                container('shell') {
                    sh """
                    ls -l *.fits
                       """
                }
            }
        }
    }
}
