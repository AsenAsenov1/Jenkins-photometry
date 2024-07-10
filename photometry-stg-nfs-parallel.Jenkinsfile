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
                    sh '''
                        pwd
                        cd ${images_dir}
                        pwd
                        ls *.fits > list

                        # Count the number of lines in 'list'
                        total_files=$(wc -l < list)

                        # Calculate half of the total number of files (rounded up)
                        half=$(( (total_files + 1) / 2 ))

                        # Use split to divide the list into two parts based on the number of lines
                        split -l ${half} list agent_
                        rm list

                        # Run Prepare function
                        pp_prepare *.fits
                    '''
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
                            sh '''
                                cd ${images_dir}
                                # Run Register function for Agent-1
                                cat agent_aa | xargs -d '\\n' pp_register
                            '''
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
                            sh '''
                                cd ${images_dir}
                                # Run Register function for Agent-2
                                cat agent_ab | xargs -d '\\n' pp_register
                            '''
                        }
                    }
                }
            }
        }

        // Use the same agent configuration for subsequent stages
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
                    sh '''
                        cd ${images_dir}
                        # Run Photometry function
                        pp_photometry *.fits
                    '''
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
                    sh '''
                        cd ${images_dir}
                        # Run Calibrate function
                        pp_calibrate *.fits
                    '''
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
                    sh '''
                        cd ${images_dir}
                        # Run Distill function
                        pp_distill *.fits
                    '''
                }
            }
        }
    }
}
