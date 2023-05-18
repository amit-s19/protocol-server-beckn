node() {
    withCredentials([string(credentialsId: 'docker_server', variable: 'docker_server')]) {
        properties([
            parameters([
                string(name: 'docker_repo', defaultValue: 'samagragovernance/protocol-server-client', description: 'Docker Image Name'),
                string(name: 'docker_server', defaultValue: "$docker_server", description: 'Docker Registry URL'),                
            ])
        ])
    }
    stage('Checkout') {
            cleanWs()
            checkout scm
            env.commit_id = env.BRANCH_NAME
            echo "${env.commit_id}"
    }

    stage('docker-build') {
        sh '''
        docker build -f protocol-server-client/Dockerfile -t $docker_server/$docker_repo:$commit_id protocol-server-client
        '''
        if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
            sh '''
            docker build -f protocol-server-client/Dockerfile -t $docker_server/$docker_repo:latest protocol-server-client
            '''
        }
    }

    stage('docker-push') {
        sh '''
        docker push $docker_server/$docker_repo:$commit_id
        '''
        if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
            sh '''
            docker push $docker_server/$docker_repo:latest
            '''
        }
    }
    
    stage('Start deploy job with latest tag') {
         if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') { 
                build job: 'Konnect/deploy-dev/protocol-server-client/', parameters: [string(name: 'tag', value: 'latest')]
         }
    }
}