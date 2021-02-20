pipeline {
    agent {
        label 'linux'
    }

    stages {
        stage('Fetch') {
            steps {
                echo 'Download source code..'
                def dante_version = "1.4.2"
                def dante_archive = "dante-${dante_version}.tar.gz"
                def response = httpRequest "https://www.inet.no/dante/files/${dante_archive}"
                writeFile file: dante_archive, text: response.content
            }
        }
    }
}
