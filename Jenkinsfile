def dante_version = "1.4.2"
def dante_archive = "dante-${dante_version}.tar.gz"

pipeline {
    agent {
        label 'linux'
    }

    stages {
        stage('Clean') {
            steps {
                cleanWs()
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
                    def dante_dir = "dante-${dante_version}"
                    sh "cp ${dante_archive} ${dante_dir}/SOURCES"
                    dir(dante_dir) {
                        def build_directory = sh (script: 'pwd', returnStdout: true).trim()
                        sh "rpmbuild  --define '_topdir ${build_directory}' -ba SPECS/dante.spec"
                    }
        
                }
            }
        }
    }
}
