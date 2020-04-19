pipeline {
    options { timeout(time: 25, unit: 'MINUTES') }
    agent any
    environment {
        CONTAINER_NAME='elk'
        ES_CONTAINER_NAME='elasticsearch'
        HOST_ES_PORT=9200
        CONTAINER_ES_PORT=9200
        LS_CONTAINER_NAME='logstash'
        HOST_LS_PORT=5044
        CONTAINER_LS_PORT=5044
        ECR_ADDRESS=credentials('ECR_ADDRESS')
        AWS_PROFILE='default'
        CONFIG='deploy'
    }
    stages {
//         stage ('Build ES') {
//             steps {
//                 sh "docker build -t $CONTAINER_NAME:elasticsearch -f ./docker/elasticsearch/Dockerfile.elasticsearch . "
//             }
//         }
//         stage ('Push Image ES') {
//             steps {
//                 script {
//                     if (env.GIT_BRANCH == 'origin/master') {
//                         sh "\$(/root/.local/bin/aws ecr get-login --no-include-email --region eu-central-1 --profile $AWS_PROFILE)"
//                         sh "docker tag $CONTAINER_NAME:elasticsearch $ECR_ADDRESS:elasticsearch"
//                         sh "docker push $ECR_ADDRESS:elasticsearch"
//                         sh "echo \"Delete image\""
//                         sh "docker image rm -f ${CONTAINER_NAME}:elasticsearch && docker image prune -f"
//                     }
//                 }
//             }
//         }
//         stage('Deploy ES') {
//             steps {
//                 script {
//                     if (env.GIT_BRANCH == 'origin/master') {
//                         sshPublisher(publishers: [sshPublisherDesc(configName: env.CONFIG, transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: "\$(aws ecr get-login --no-include-email --region eu-central-1 ); docker pull ${ECR_ADDRESS}:elasticsearch; docker rm -f ${ES_CONTAINER_NAME} ; docker run -e \"discovery.type=single-node\" -e \"ES_JAVA_OPTS=-Xms100m -Xmx100m\" --name ${ES_CONTAINER_NAME} -d -p ${HOST_ES_PORT}:${CONTAINER_ES_PORT} ${ECR_ADDRESS}:elasticsearch", execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
//                     }
//                 }
//             }
//         }
        stage ('Build Logstash') {
            steps {
                sh "docker build -t $CONTAINER_NAME:logstash -f ./docker/logstash/Dockerfile . "
            }
        }
        stage ('Push Image Logstash') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sh "\$(/root/.local/bin/aws ecr get-login --no-include-email --region eu-central-1 --profile $AWS_PROFILE)"
                        sh "docker tag $CONTAINER_NAME:logstash $ECR_ADDRESS:logstash"
                        sh "docker push $ECR_ADDRESS:logstash"
                        sh "echo \"Delete image\""
                        sh "docker image rm -f ${CONTAINER_NAME}:logstash && docker image prune -f"
                    }
                }
            }
        }
        stage('Deploy Logstash') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/master') {
                        sshPublisher(publishers: [sshPublisherDesc(configName: env.CONFIG, transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: "\$(aws ecr get-login --no-include-email --region eu-central-1 ); docker pull ${ECR_ADDRESS}:logstash; docker rm -f ${LS_CONTAINER_NAME} ; docker run -e \"ES_JAVA_OPTS=-Xms100m -Xmx100m\" --name ${LS_CONTAINER_NAME} -d -p ${HOST_LS_PORT}:${CONTAINER_LS_PORT} ${ECR_ADDRESS}:logstash", execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
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