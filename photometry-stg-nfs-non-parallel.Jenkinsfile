pipeline {
    agent {
        kubernetes {
            label 'photometry-agent-nfs'
            defaultContainer 'shell'
            nodeSelector 'kubernetes.io/hostname=worker-01'
            retries 2
        }
    }

    environment {
        images_dir = '/pvc-volume/pipeline-images'
    }

    stages {
        stage('Prepare') {
            steps {
                container('shell') {
                    sh '''
                        pwd
                        cd ${images_dir}
                        pp_prepare *.fits
                    '''
                }
            }
        }

        stage('Register') {
            steps {
                container('shell') {
                    sh '''
                        cd ${images_dir}
                        # Run Register function 
                         pp_register *.fits
                    '''
                }
            }
        }

        stage('Photometry') {
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
