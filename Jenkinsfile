pipeline {
    agent any
    parameters {
        choice(name:'Account', choices: ['dev', 'qa', 'stage', 'prod'], description: "Pick Env")
    }
    environment{
        RegistryURL = "https://registry.hub.docker.com/"
        RepoName = "sarangp007/sarang_cloudethix_nginx"
        dh_creds = 'dockerhub_creds'
    }
    stages{
        stage('Builing image in Dev') {
           when expression{
                params.Account == "dev"
            }
            environment{
                registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
                tag = "${env.RepoName}" + ':' + "dev_$GIT_COMMIT"
                file_path = "${workspace}/"
            }
            steps{
                script{
                     docker.withRegistry(registry_endpoint, dh_creds) {

                     def Image = docker.build(tag, file_path)

                     /* Push the container to the custom Registry */
                     Image.push()

                 }
            }

        }
    }
    stage('Push the Docker Image in QA') {
                when {
                    expression {
                        params.account == 'qa'
                    }
                }
                    environment {
                        dev_registry_endpoint = 'https://' + "${env.registryURI}" + "${env.dev_registry}"
                        qa_registry_endpoint  = 'https://' + "${env.registryURI}" + "${env.qa_registry}"
                        dev_image             = "${registryURI}" + "${env.dev_registry}" + ':' + "${env.COMMITID}"
                        qa_image              = "${registryURI}" + "${env.qa_registry}" + ':' + "${env.COMMITID}"
                    }
                    steps {
                        script {
                            docker.withRegistry(dev_registry_endpoint, dev_dh_creds) {
                                docker.image(dev_image).pull()
                            }

                            sh 'echo Image pulled'

                            sh "docker tag ${env.dev_image} ${env.qa_image}"

                            docker.withRegistry(qa_registry_endpoint , qa_dh_creds) {
                                docker.image(env.qa_image).push()
                            }

                            sh 'echo Image pushed'
                        }
                    }
                    post {
                        always {
                            sh 'echo Cleaning docker Images from Jenkins.'
                            sh "docker rmi ${env.dev_image}"
                            sh "docker rmi ${env.qa_image}"
                        }
                    }
            }
        }
 }  
}
//https://hub.docker.com/r/sarangp007/jenkins_docker