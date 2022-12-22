pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      label 'jenkins-slave'
      defaultContainer 'jnlp'
      yamlFile """
 apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  -name: docker
   env:
   - name: DOCKER_HOST
     value: 127.0.0.1
     image: docker
     command:
     - cat:
     tty: true
  - name: tools
    image: nekottyo/kustomize-kubeval
    command:
    - cat: 
    tty: true
  - name: kubectl
    image: joshendriks/alpine-k8s
    command:
    - /bin/cat
    tty: true    
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: config.json
      """
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
