pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build auto'
                sh './gradle build --no-daemon'

                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
