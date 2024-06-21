pipeline {
    agent { label 'nodejs' }

    // Set your OCP project
    environment { APP_NAMESPACE = 'jcasop-devsecops' }

    stages{

        stage('Test'){
            steps {
                sh "node test.js"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    oc start-build greeting-devsecops \
                    --follow --wait -n ${APP_NAMESPACE}
                '''
            }
}
}
post {
        failure {
            withCredentials([usernamePassword(
                credentialsId: 'github-global',
                usernameVariable: 'USERNAME',
                passwordVariable: 'PASSWORD'
            )]) {
                sh """
                    curl -X POST \
                    -H 'Authorization: token $PASSWORD' \
                    'https://api.github.com/repos/$USERNAME/do400-greeting-devsecops/issues' \
                    -d '{"title": "CI build $BUILD_NUMBER", "body": "Pipeline build $BUILD_NUMBER has failed"}'
                """
            }
        }
    }
}
}
