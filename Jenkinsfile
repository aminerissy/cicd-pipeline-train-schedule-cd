def remote = [:]
remote.name = "node"
remote.host = "node.abc.com"
remote.allowAnyHosts = true

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/app.zip'
            }
        }
        
        //stage('DeployToStaging') {
        //    when {
        //        branch 'master'
        //    }
        //    steps {
        //        withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
        //            sshPublisher(
        //                failOnError: true,
        //                continueOnError: false,
        //                publishers: [
        //                    sshPublisherDesc(
        //                        configName: 'staging',
        //                        sshCredentials: [
        //                            username: "$USERNAME",
        //                            encryptedPassphrase: "$USERPASS"
        //                        ], 
        //                        transfers: [
        //                            sshTransfer(
        //                                sourceFiles: 'dist/trainSchedule.zip',
        //                                removePrefix: 'dist/',
        //                                remoteDirectory: '/tmp',
        //                                execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
        //                            )
        //                        ]
        //                    )
        //                ]
        //            )
        //        }
        //    }
        //}
        
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'production_server_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {  
                    //remote.user = "$USERNAME"
                    //remote.password = "$USERPASS"
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                verbose: true,
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/app.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo systemctl stop trainSchedule && rm -rf /opt/app/* && sudo apt install unzip && unzip /tmp/app.zip -d /opt/app && sudo systemctl start trainSchedule'  
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
     
