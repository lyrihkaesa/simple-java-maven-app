node {
    docker.image('maven:3.9.6-eclipse-temurin-17-alpine').inside('-v /root/.m2:/root/.m2') {
        stage('Build'){
            sh 'mvn -B -DskipTests clean package'
        }
        stage('Test'){
            sh 'mvn test'
            junit 'target/surefire-reports/*.xml'
        }
        stage('Manual Approval'){
            input message: 'Lanjutkan ke tahap Deploy?'
        }
        stage('Deploy'){
            sh './jenkins/scripts/deliver.sh'
            sleep(time:1, unit:"MINUTES")
         
            // Extract project name and version from pom.xml
            def NAME = sh(script: 'mvn help:evaluate -Dexpression=project.name | grep "^[^\\[]"', returnStdout: true).trim()
            def VERSION = sh(script: 'mvn help:evaluate -Dexpression=project.version | grep "^[^\\[]"', returnStdout: true).trim()

            echo 'Use Publish Over SSH plugin to upload JAR file to VPS'
            sshPublisher(
                publishers: [
                    sshPublisherDesc(
                        configName: 'vps-ubuntu', // Nama konfigurasi SSH yang sudah Anda set
                        transfers: [
                            sshTransfer(
                                sourceFiles: "target/${NAME}-${VERSION}.jar",
                                removePrefix: 'target/',
                                remoteDirectory: '/prod/',
                            )
                        ]
                    )
                ]
            )

            echo 'Run the application on VPS'
            sshPublisher(
                publishers: [
                    sshPublisherDesc(
                        configName: 'vps-ubuntu', // Nama konfigurasi SSH yang sudah Anda set
                        transfers: [
                            sshTransfer(
                                sourceFiles: "",
                                execCommand: "java -jar ./prod/${NAME}-${VERSION}.jar | tee apps-logs.txt && sudo journalctl -n 1000 > logs-${NAME}-${VERSION}.txt"
                            )
                        ]
                    )
                ]
            )
        }
    }
}
