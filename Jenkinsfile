pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=shtlamrut/jenkins:${BUILD_NUMBER}
            '''
          }
        }
      }
    }

   // stage('Deploy App to Kubernetes') {     
     // steps {
       // container('kubectl') {
         // withCredentials([file(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG')]) {
           // sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" myweb.yaml'
            //sh 'kubectl apply -f myweb.yaml'
          //}
        //}
      //}
    //}
      stage('Deploy qa') {
      environment {
        GIT_CREDS = credentials('github')
      }
      steps {
        container('tools') {
          sh "git clone https://$GIT_CREDS_USR:$GIT_CREDS_PSW@github.com/shtlamrut/argocd-demo.git"
          sh "git config --global user.email shtlamrut@gmail.com"
          sh "git config --global user.name shtlamrut"
        }
      }
      }
  }
}
