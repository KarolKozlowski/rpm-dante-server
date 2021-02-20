pipeline {
    agent {
        label 'linux'
    }

    stages {
        stage('Fetch') {
            steps {
                script {
                    echo 'Download source code..'
                    def dante_version = "1.4.2"
                    def dante_archive = "dante-${dante_version}.tar.gz"
                    def response = httpRequest(
                            url: "https://www.inet.no/dante/files/${dante_archive}",
                            outputFile: dante_archive
                    )
                }
            }
        }
    }
}
