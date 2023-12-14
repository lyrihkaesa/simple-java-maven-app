node {
    docker.image('maven:3.9.6-eclipse-temurin-17-alpine').inside('-v /root/.m2:/root/.m2') {
        
        def buildStatus = 'SUCCESS' // Assume success initially

        stage('Build') {
            buildStatus = sh(script: 'mvn -B -DskipTests clean package', returnStatus: true) == 0 ? 'SUCCESS' : 'FAILURE'
        }

        stage('Test') {
            buildStatus = sh(script: 'mvn test', returnStatus: true) == 0 ? 'SUCCESS' : 'FAILURE'

            if (buildStatus == 'SUCCESS') {
                junit 'target/surefire-reports/*.xml'
            }
        }

        stage('Deliver') {
            if (buildStatus == 'SUCCESS') {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
