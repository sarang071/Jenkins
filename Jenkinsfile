pipeline {
    agent any
    parameters {
        choice(name:'Account', choices: ['dev', 'qa', 'stage', 'prod'], description: "Pick Env")
        string(name: 'CommitID', defaultValue:'latest' , description: "Give Commit ID")
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
            dev_image             = "${env.RepoName}" + ":$params.CommitID"
            qa_image              = "${env.RepoName}" + ":qa_$params.CommitID"
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
    stage('Pushing to Stage'){
        when { 
            expression {
            params.Account == "stage"
          }
        }
        environment{
            qa_registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
            stage_registry_endpoint  = "${env.RegistryURL}" + "${env.RepoName}"
            qa_image              = "${env.RepoName}" + ":qa_$params.CommitID"
            stage_image           = "${env.RepoName}" + ":stage_$params.CommitID"
        }
    steps {
        script {
                docker.withRegistry(qa_registry_endpoint, dh_creds) {
                docker.image(qa_image).pull()
                }
                 sh 'echo Image pulled'
                 sh "docker tag ${env.qa_image} ${env.stage_image}"
                 docker.withRegistry(stage_registry_endpoint, dh_creds) {
                 docker.image(env.stage_image).push()
                }
                sh 'echo Image pushed'
            }
        }
    }
    stage('Pushing to Prod'){
        when { 
            expression {
            params.Account == "prod"
          }
        }
        environment{
            stage_registry_endpoint = "${env.RegistryURL}" + "${env.RepoName}"
            prod_registry_endpoint  = "${env.RegistryURL}" + "${env.RepoName}"
            stage_image             = "${env.RepoName}" + ":stage_$params.CommitID"
            prod_image              = "${env.RepoName}" + ":prod_$params.CommitID"
        }
    steps {
        script {
                docker.withRegistry(stage_registry_endpoint, dh_creds) {
                docker.image(stage_image).pull()
                }
                 sh 'echo Image pulled'
                 sh "docker tag ${env.stage_image} ${env.prod_image}"
                 docker.withRegistry(prod_registry_endpoint, dh_creds) {
                 docker.image(env.prod_image).push()
                }
                sh 'echo Image pushed'
            }
        }
    }
  }
}
//https://hub.docker.com/r/sarangp007/jenkins_docker