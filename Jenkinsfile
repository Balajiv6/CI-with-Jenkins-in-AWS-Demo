pipeline {

    agent any

    environment {

        PROJECT_ID = 'balajitrail-1479377582175'

        CLUSTER_NAME = 'c2-kub-525167'

        LOCATION = 'us-west1-b'

        CREDENTIALS_ID = 'K8'

    }

    stages {

        stage("Checkout code") {

            steps {

                checkout scm

            }

        }

		 stage("Build Web app") {

            steps {

               echo "cleaning and packaging"

			   sh 'mvn clean package'

            }

        }


        stage("Build image") {

            steps {

                script {

                    myapp = docker.build("balajiv6/k8s:${env.BUILD_ID}")

                }

            }

        }

        stage("Push image") {

            steps {

                script {

                    docker.withRegistry('https://registry.hub.docker.com', 'docker') {

                            myapp.push("${env.BUILD_ID}")

                    }

                }

            }

        }        

        stage('Deploy to Google Kubernetes Engine') {

            steps{

			    echo "Deployment started"

				sh 'ls -ltr'

				sh 'pwd'

                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"

                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])

				echo "Deployment Finished"

            }

        }

    }    

}

