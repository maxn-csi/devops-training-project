pipeline{
    agent any
    stages{
        stage('Checkout SCM'){
            steps{
                script{
                    def scmVars = checkout([$class: 'GitSCM',
                            branches: [[name: "dev"]],
                            userRemoteConfigs: [[
                                credentialsId: 'Github',
                                url: 'https://github.com/maxn-csi/devops-training-project']]
                            ])
                    env.GIT_COMMIT = "${scmVars.GIT_COMMIT}"
                }
            }
        }
        stage("Gradle build"){
            steps{
                dir("backend") {
                    sh "./gradlew build -x test"
                }
            }
        }
        // stage("test"){
        //     steps{
        //         dir("backend") {
        //             sh "./gradlew test --debug"
        //         }
        //     }
        // }
        stage('SonarQube analysis') {
            steps{
                withSonarQubeEnv('sonarqube') {
                    dir("backend") {
                        sh './gradlew sonarqube -x test'
                    }
                }
            }
        }
        stage("Publish") {
            steps {
                dir("backend") {
                    nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'maven-releases', packages:
                        [[$class: 'MavenPackage', mavenAssetList:
                            [[classifier: '', extension: '', filePath: './build/libs/backend-0.0.1-SNAPSHOT.jar']],
                            mavenCoordinate: [artifactId: 'backend', groupId: 'devops-training-project', packaging: 'jar', version: "${BUILD_NUMBER} ${BUILD_TIMESTAMP} ${env.GIT_COMMIT}"]]]
                }
            }
        }
        stage("Deploy") {
            steps {
                dir("backend") {
                    sshPublisher(publishers:
                        [sshPublisherDesc(configName: 'backend01', transfers:
                            [sshTransfer(cleanRemote: false, excludes: '', execCommand:
                                "sudo mv /home/ubuntu/artifact/backend-0.0.1-SNAPSHOT.jar /devops-training-project/backend/build/libs/SNAPSHOT_${BUILD_NUMBER}.jar",
                                execTimeout: 120000,
                                flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+',
                                remoteDirectory: 'artifact', remoteDirectorySDF: false,
                                removePrefix: 'build/libs/', sourceFiles: 'build/libs/backend-0.0.1-SNAPSHOT.jar')],
                            usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                }
            }
        }
    }
}
