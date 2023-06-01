pipeline {
    agent any
    parameters {
        choice(name:'Account', choices: ['dev', 'qa'], description: "Pick Env")
        string(name: 'CommitID', default:'latest' , description: "Give Commit ID")
    }
    environment{
        RegistryURL = "https://registry.hub.docker.com/"
        RepoName = "sarangp007/sarang_cloudethix_nginx"
        dh_creds = 'dockerhub_creds'
    }
    stages{
        stage('Builing image in Dev') {
           when {
            expression {
                params.Account == 'dev'
            }
           }
            environment{
                registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
                tag = "${env.RepoName}" + ':' + "$GIT_COMMIT"
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
    stage('Pushing to QA'){
        when { 
            expression {
            params.Account == "qa"
          }
        }
        environment{
            dev_registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
            qa_registry_endpoint  = "${env.RegistryURL}" + "${env.RepoName}"
            dev_image             = "${env.RegistryURL}" + "${env.RepoName}" + ':' + "$params.CommitID"
            qa_image              = "${env.RegistryURL}" + "${env.RepoName}" + ':' + "$params.CommitID"
        }
    steps {
        script {
                docker.withRegistry(dev_registry_endpoint, dh_creds) {
                docker.image(dev_image).pull()
                }
                 sh 'echo Image pulled'
                 sh "docker tag ${env.dev_image} ${env.qa_image}"
                 docker.withRegistry(qa_registry_endpoint, dh_creds) {
                 docker.image(env.qa_image).push()
                }
                sh 'echo Image pushed'
            }
        }
    }
  }
}
//https://hub.docker.com/r/sarangp007/jenkins_docker