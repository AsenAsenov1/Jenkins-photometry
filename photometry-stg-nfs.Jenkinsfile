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
                    sh """
                        pwd
                        cd ${env.images_dir}
                        pwd
                        ls *.fits > list && split -n 2 list agent_ && rm list

                        # Run Prepare function
                        pp_prepare *.fits
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

                                # Run Register function for Agent-1
                                cat agent_aa | xargs pp_register
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

                                # Run Register function for Agent-2
                                cat agent_ab | xargs pp_register
                            """
                        }
                    }
                }
            }
        }

        // Continue using the same agent

        stage('Photometry') {
            steps {
                container('shell') {
                    sh """
                        cd ${env.images_dir}

                        # Run Photometry function
                        pp_photometry *.fits
                    """
                }
            }
        }

        stage('Calibrate') {
            steps {
                container('shell') {
                    sh """
                        cd ${env.images_dir}

                        # Run Calibrate function
                        pp_calibrate *.fits
                    """
                }
            }
        }

        stage('Distill') {
            steps {
                container('shell') {
                    sh """
                        cd ${env.images_dir}

                        # Run Distill function
                        pp_distill *.fits
                    """
                }
            }
        }
    }
}
