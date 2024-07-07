pipeline {
    agent none  // No default agent for the whole pipeline

    environment {
        images_dir = '/pvc-volume/pipeline-images'
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
