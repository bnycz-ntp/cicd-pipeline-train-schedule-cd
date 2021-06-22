pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage("DeployToStaging"){
            when {
                branch 'master'
            }
            steps{
                echo "====++++executing DeployToStaging++++===="
                withCredentials([usernamePassword(credentialsId: '8705a6fd-5afb-429d-8a48-738730f21191', usernameVariable: 'USERNAME_zmienna', passwordVariable: 'USERPASS_zmienna')])
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME_zmienna",
                                    encryptedPassphrase: "$USERPASS_zmienna"
                                ],
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                )
                            ]
                            )
                        ]
                    )
                when {
                    branch 'master'
                }
            }
            post{
                withCredentials([usernamePassword(credentialsId: '8705a6fd-5afb-429d-8a48-738730f21191')])
                always{
                    echo "====++++always++++===="
                }
                success{
                    echo "====++++DeployToStaging executed successfully++++===="
                    when {
                        branch 'master'
                    }
                }
                failure{
                    withCredentials([usernamePassword(credentialsId: '8705a6fd-5afb-429d-8a48-738730f21191')])
                    echo "====++++DeployToStaging execution failed++++===="
                    when {
                        branch 'master'
                    }
                }
        
        withCredentials([usernamePassword(credentialsId: '')])
            }
        }
    }
}
