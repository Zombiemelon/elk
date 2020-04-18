pipeline {
    options { timeout(time: 25, unit: 'MINUTES') }
    agent any
    environment {
        CONTAINER_NAME='elk'
        ES_CONTAINER_NAME='elasticsearch'
        HOST_ES_PORT=9200
        CONTAINER_ES_PORT=9200
        ECR_ADDRESS=credentials('ECR_ADDRESS')
        AWS_PROFILE='default'
        CONFIG='deploy'
    }
    stages {
        stage ('Build Back') {
            steps {
                sh "docker build -t $CONTAINER_NAME:elasticsearch -f ./docker/elasticsearch/Dockerfile.elasticsearch . "
            }
        }
        stage ('Push Image Back') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh "\$(/root/.local/bin/aws ecr get-login --no-include-email --region eu-central-1 --profile $AWS_PROFILE)"
                        sh "docker tag $CONTAINER_NAME:elasticsearch $ECR_ADDRESS:elasticsearch"
                        sh "docker push $ECR_ADDRESS:elasticsearch"
                        sh "echo \"Delete image\""
                        sh "docker image rm -f ${CONTAINER_NAME}:elasticsearch && docker image prune -f"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sshPublisher(publishers: [sshPublisherDesc(configName: env.CONFIG, transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: "\$(aws ecr get-login --no-include-email --region eu-central-1 ); docker pull ${ECR_ADDRESS}:elasticsearch; docker rm -f ${ES_CONTAINER_NAME} ; docker run -e \"discovery.type=single-node\" -e \"ES_JAVA_OPTS=-Xms100m -Xmx100m\" --name ${ES_CONTAINER_NAME} -d -p ${HOST_ES_PORT}:${CONTAINER_ES_PORT} ${ECR_ADDRESS}:elasticsearch", execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
            sh 'docker system prune -f'
        }
     }
}