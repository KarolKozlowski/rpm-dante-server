def dante_version = "1.4.2"
def dante_archive = "dante-${dante_version}.tar.gz"

pipeline {
    agent {
        label 'linux'
    }

    stages {
        stage('Clean') {
            steps {
                script {
                    sh "rm -rf ./dante-${dante_version}"
                }
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
                        sh 'pwd'
                        sh 'ls -la'
                        sh "rpmbuild  --define '_topdir $(pwd)' -bb SPECS/dante.spec"
                    }
        
                }
            }
        }
    }
}
