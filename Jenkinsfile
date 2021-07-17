List<String> CHOICES = [];
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh 'gradle build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build and Scan Docker Image') {
            steps {
                script {
                    app = docker.build("nicholasgull/train-schedule")
                    app.inside {
                        sh """
                        echo \$(curl localhost:8080)
                        """
                    }
                }
                sh """
                   /usr/local/bin/snyk config set api=932b137e-6b1e-49b3-bedb-d7f589472540
                   /usr/bin/docker scan nicholasgull/train-schedule
                   /usr/local/bin/snyk monitor
                   ./scanscript
                   """
        }
        }
        stage('User Input') {

            steps {
                script {

            
                        CHOICES = ["Deploy", "DoNotDeploy"];   

                        env.YourTag = input  message: 'Do you want to Deploy the Image?',ok : 'Deploy',id :'tag_id',

                                        parameters:[choice(choices: CHOICES, description: 'Select a tag for this build', name: 'POLICY PASSED')]

                        }          

                echo "Deploying ${env.YourTag}. Deploy Image."

            }

        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dhubtoken') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('CanaryDeploy') {
            environment {
                CANARY_REPLICAS = 1
            }
            steps {
                sh """
                gcloud container clusters get-credentials nick-one-kub-healthcheck --zone us-central1-c --project devsecops-311418
                pwd
                ls
                cd /var/lib/jenkins/workspace/auto-deploy-trainschedule-app
                pwd
                ls
                kubectl apply -f /var/lib/jenkins/workspace/auto-deploy-trainschedule-app/train-schedule-kube-canary.yml --validate=false
                kubectl get services
                """
            }
        }
    }
}
