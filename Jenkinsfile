def dante_version = "1.4.3"
def dante_archive = "dante-${dante_version}.tar.gz"
def dante_dir = "dante-${dante_version}"

def job_name = JOB_NAME.tokenize('/') as String[]
def proj_name = job_name[1]

RPM_GLOB = "**/RPMS/x86_64/*.rpm"

pipeline {
    agent none
    stages {
        stage('Build RPM') {
            agent {
                label 'rhel8'
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
                                stash name: "rpms", includes: RPM_GLOB
                                archiveArtifacts artifacts: RPM_GLOB, fingerprint: true
                            }
                
                        }
                    }
                }
            }
        }
        stage('Release RPM') {
            agent {
                label 'yumrepo'
            }
            steps {
                script {
                    echo 'Release rpm...'
                    dir(dante_dir) {
                        job_name = "${env.JOB_NAME}"
                        repo_base = "/srv/mirror/dotnot.pl"
                        repo_dir = "${repo_base}/ci/${proj_name}/"

                        unstash name: "rpms"
                        sh "mkdir -p ${repo_dir}"
                        def rpms = findFiles(glob: RPM_GLOB)
                        rpms.each { item ->
                            sh "cp ${item.path} ${repo_dir}"
                        }
                        sh "createrepo ${repo_base}"
                    }
                }
            }
        }
    }
}
