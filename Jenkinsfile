pipeline{
  agent {
    dockerfile {
      dir 'agent'
      filename 'Dockerfile'
      label 'build-server-agent'
      registryCredentialsId '6b2d0b83-9cca-4d23-b69b-bcf247bc8379'
      registryUrl '104.198.236.144:8123'
    }
  }    
  stages{
    stage('Copy source with configs') {
      steps {
        git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        sh 'ssh-keyscan -H node-1 >> ~/.ssh/known_hosts'
        // sh 'scp jenkins@devbuild-srv01:/home/jenkins/build/configs/staging/gateway-api/application-business-config-defaults.yml gateway-api/src/main/resources/application-business-config-defaults.yml'
      }
      post{
          // always{
          //     echo "========always========"
          // }
          success{
              echo "========A executed successfully========"
          }
          failure{
              echo "========A execution failed========"
          }
      }
    }
    stage('Build app') {
      steps {
        withMaven(mavenLocalRepo: 'prod/.maven_repo') {
          sh 'mvn package'
        }
      }
      post{
        success{
          chuckNorris()
        }
        failure{
            echo "========A execution failed========"
        }
      }
    }
    stage('Build and push prod docker image') {
      steps {
        sh 'cd prod'
        sh 'docker build --tag prodserver'
        sh 'docker tag prodserver nexus:8123/prodserver:latest'
        docker.withRegistry('nexus:8123', '6b2d0b83-9cca-4d23-b69b-bcf247bc8379') {
          docker.image('prodserver').push('latest')
        }        
        // sh 'docker login -u admin -p nucsfvkmpp nexus:8123'
        // sh 'docker push nexus:8123/prodserver:latest'
      }
      post{
        success{
          chuckNorris()
        }
        failure{
            echo "========A execution failed========"
        }
      }
    }
    stage('Run prod docker container on node-1') {
      steps {
        sh 'ssh-keyscan -H node-1 >> ~/.ssh/known_hosts'
        sh 'ssh root@node-1 << EOF'
        sh 'docker stop prodserver'
        sh 'docker run -d --rm --name prodserver -p 80:8080 nexus:8123/prodserver:latest'
        sh 'EOF'
      }
      post{
        success{
          chuckNorris()
        }
        failure{
            echo "========A execution failed========"
        }
      }
    }
  }
  post{
      always{
          echo "========always========"
      }
      success{
          echo "========pipeline executed successfully ========"
      }
      failure{
          echo "========pipeline execution failed========"
      }
  }
}
