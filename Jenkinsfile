pipeline {
    agent any
    
    options {
        timestamps()
        timeout(time:15, unit:'MINUTES')
        buildDiscarder(logRotator(
            numToKeepStr: '8',
            daysToKeepStr: '10',
            artifactNumToKeepStr: '30'))
    }

    stages {
        stage('deploy') {
            steps {
                script {
                    sshagent(credentials: ['ssh-github']) {
                        def filename = 'todo/values.yaml'
                        def data = readYaml (file: filename)
                        data.image.tag = env.MY_PARAM

                        sh "rm $filename"
                        writeYaml file: filename, data: data

                        filename = 'nginx/values.yaml'
                        data = readYaml (file: filename)
                        data.image.tag = env.MY_PARAM
                        
                        sh "rm $filename"
                        writeYaml file: filename, data: data

                        sh """
                            git add .
                            git commit -am "Updated app+nginx image tag to ${env.MY_PARAM}"
                            git push
                        """
                    }
                }
            }
        }
    }

    post {
        failure {
            sh "echo ==== DESTROY ====="
        }
    }
}