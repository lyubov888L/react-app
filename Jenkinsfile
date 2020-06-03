pipeline {
   agent any
   environment {
           ENV_NAME = getEnvName(env.BRANCH_NAME)
        }
    stages {
       stage('Push Image') {
         steps {
             echo '$ENV_NAME'
             echo 'Push image to dockerhub'
             script {
              
             withDockerRegistry(credentialsId: 'bhavikdocker', url: '') {
                def app = docker.build("bhavik0907/react-app:${ENV_NAME}${env.BUILD_ID}") 
             app.push()
             }
             }
         }  
       }
        stage('Kubernetes deployment') {
           steps{
            echo "Deploy %{env.BUILD_ID}% now ${env.BUILD_ID}"
            script {
                bat 'echo %env.BUILD_ID% now ${env.BUILD_ID} again %{env.BUILD_ID}%'
                bat 'echo %BUILD_NUMBER% or %BUILD_ID%'
                bat 'kubectl --kubeconfig ./config apply -f kubernetes_new/config_%ENV_NAME%.yaml -n %ENV_NAME%' 
                bat 'kubectl --kubeconfig ./config apply -f kubernetes_new/deployment.yaml -n %ENV_NAME%' 
                bat 'kubectl --kubeconfig ./config set image deployment/react-app-example react-app-example=bhavik0907/react-app:%ENV_NAME%%BUILD_ID% -n %ENV_NAME%'   
                bat 'kubectl --kubeconfig ./config apply -f kubernetes_new/service_%ENV_NAME%.yaml -n %ENV_NAME%'  
                  
            }
           }
       }
    }
      }
def getEnvName(branchName) {
    if("master".equals(branchName)) {
        return "preprod";
    } else if ("release".equals(branchName)) {
        return "preprod";
    } else {
        return "dev";
    }
}
