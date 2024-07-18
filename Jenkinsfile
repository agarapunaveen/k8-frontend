pipeline {
    agent {
        label 'agent-1'
    }
    options {
        // Timeout counter starts AFTER agent is allocated
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
   
    environment{
       def appVersion = ''
       def nexusUrl= 'nexus.naveencloud.online:8081'
       def region='us-east-1'
       def account_id='211125718262'
    }
    stages {
        stage('read the version'){
            steps{

              script { 
                def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                
                }
            }
        }
        // stage('Install dependencies') {
        //     steps {
        //         sh """
        //          npm install
        //          ls -ltr
        //         """
        //     }
        // }
        stage ('Docker Build'){
            steps{
                sh """
                  aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${account_id}.dkr.ecr.${region}.amazonaws.com

                  docker build -t expense-frontend .

                  docker tag expense-backend:latest ${account_id}.dkr.ecr.us-east-1.amazonaws.com/expense-frontend:${appVersion}

                  docker push ${account_id}.dkr.ecr.us-east-1.amazonaws.com/expense-frontend:${appVersion}
                """
            }
        }

         stage ('Deploy'){
            steps{
                sh """
                  aws eks update-kubeconfig --region us-east-1 --name expense-dev
                  cd helm
                  sed -i 's/IMAGE_VERSION/${appVersion}/g' values.yaml
                  helm install frontend . 
                """
            }
        }

        // stage('nexus artifact upload'){
        //     steps{
        //       script{
        //         nexusArtifactUploader(
        //         nexusVersion: 'nexus3',
        //         protocol: 'http',
        //         nexusUrl: "${nexusUrl}",
        //         groupId: 'com.example',
        //         version: "${appVersion}",
        //         repository: "frontend",
        //         credentialsId: 'nexus',
        //         artifacts: [
        //             [artifactId: "frontend",
        //             classifier: '',
        //             file: 'frontend-' + "${appVersion}" + '.zip',
        //             type: 'zip']
        //         ]
        //     )
        //       }
        //     }
        // }
        // stage("deploy"){
        //     steps{
               
        //         script{
        //              def params =[
        //                string(name: 'appVersion',value: "${appVersion}")
        //             ]
        //              build job: 'frontend-deploy', parameters: params,wait: false
        //         }
        //     }
        // }
    }
        post {
            always{
                echo ' i will always say hello again'
                deleteDir()
            }
            success{
                echo ' i will run the pipeline success'
            }
            failure{
                echo ' i will run the pipeline failure'
            }
        }
}