def dante_version = "1.4.2"
def dante_archive = "dante-${dante_version}.tar.gz"
def dante_dir = "dante-${dante_version}"

pipeline {
    agent {
        label 'linux'
    }

    stages {
        stage('Clean') {
            steps {
                sh 'rm -rf ${dante_dir}'
            }
        }
        stage('Fetch') {
            steps {
                script {
                    echo 'Download and extract source code...'
                    def response = httpRequest(
                            url: "https://www.inet.no/dante/files/${dante_archive}",
                            outputFile: dante_archive
                    )
                    sh "tar -xf ${dante_archive}"
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    echo 'Build rpm...'
                    sh "cp ${dante_archive} ${dante_dir}/SOURCES"
                    dir(dante_dir) {
                        def build_directory = sh (script: 'pwd', returnStdout: true).trim()
                        sh "env"
                        sh "rpmbuild --define '%_hardening_ldflags -Wl,-z,now' --define '_topdir ${build_directory}' --define '_extraflags #' -ba SPECS/dante.spec"
                    }
        
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: '**/RPMS/x86_64/*.rpm', fingerprint: true
        }
    }
}
