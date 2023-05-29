pipeline {
    agent any
    environment{
        RegistryURL = "https://registry.hub.docker.com/"
        RepoName = "sarangp007/sarang_cloudethix_nginx"
        dh_creds = 'dockerhub_creds'
    }
    stages{
        stage('Builing image') {
            environment{
                registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
                tag = "${env.RepoName}" + ':' + "$GIT_COMMIT"
                #file_path = "${workspace}/docker/"
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
    
    }
  
}
//https://hub.docker.com/r/sarangp007/jenkins_docker