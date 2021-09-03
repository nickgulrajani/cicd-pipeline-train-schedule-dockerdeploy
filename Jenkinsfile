pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build step  automation'
                sh './gradle build --no-daemon'

                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
