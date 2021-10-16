pipeline {
  agent {
    // dockerfile {
    //   dir './agent'
    //   filename 'Dockerfile'
    //   customWorkspace 'agent'
    // }
    dockerfile {
      // dir 'agent'
      filename './agent/Dockerfile'
      // dir './agent'
      // additionalBuildArgs '-t buildserver'
      // registryCredentialsId '6b2d0b83-9cca-4d23-b69b-bcf247bc8379'
      // registryUrl 'http://nexus:8123'
    }
  }
  stages{
    stage('Copy source with configs') {
      steps {
        git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        sh 'rm -rf ./conf'
        sh 'git clone https://github.com/artem-pvl/devops_hw_11.git ./conf'
      }
      post{
          failure{
              echo "========A execution failed========"
          }
      }
    }
    stage('Build app') {
      steps {
        withMaven {
          sh 'mvn package'
        }
      }
      post{
        failure{
            echo "========A execution failed========"
        }
      }
    }
    stage('Build and push prod docker image') {
      steps {
        script {
          docker.withRegistry('http://nexus:8123', '6b2d0b83-9cca-4d23-b69b-bcf247bc8379') {
            sh 'cp ./target/hello-1.0.war ./conf/prod'
            sh 'docker build --tag prodserver ./conf/prod'
            sh 'docker tag prodserver nexus:8123/prodserver:latest'
            docker.image('prodserver').push('latest')
            sh 'docker rmi nexus:8123/prodserver:latest'
          }
        }
      }   
      post{
        failure{
            echo "========A execution failed========"
        }
      }
    }
    stage('Run prod docker container on node-1') {
      steps {
        sh 'echo $USER'
        sshagent(['jenkins']) {
          sh 'ssh root@node-1 docker stop prodserver'
            // && docker run -d --rm --name prodserver -p 80:8080 nexus:8123/prodserver:latest'
        }        
        // sh 'touch ~/.ssh/known_hosts'
        // sh 'ssh-keyscan -H node-1 >> ~/.ssh/known_hosts'
        // sh 'ssh root@node-1 << EOF'
        // sh 'EOF'
      }
      post{
        failure{
            echo "========A execution failed========"
        }
      }
    }
  }
  post{
      success{
          chuckNorris()
      }
      failure{
          echo "||| *** ||| pipeline execution failed ||| *** |||"
      }
  }
}
