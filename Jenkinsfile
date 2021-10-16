pipeline {
  // agent {
  //   docker {
  //     alwaysPull true
  //     args '-u 0:0 -v /$USER/.ssh/id_rsa:/$USER/.ssh/id_rsa'
  //     image 'nexus:8123/buildserver:latest'
  //   }
  // }
  agent any
  stages{
    // stage('debug') {
    //   steps {
    //     sh 'id'
    //     sh 'cat /root/.ssh/id_rsa'
    //   }
    // }
    // stage('Copy source with configs') {
    //   agent {
    //     docker {
    //       alwaysPull true
    //       reuseNode true
    //       args '-u 0:0 -v /$USER/.ssh/id_rsa:/$USER/.ssh/id_rsa'
    //       image 'nexus:8123/buildserver:latest'
    //     }
    //   }
    //   steps {
    //     git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
    //     sh 'rm -rf ./conf'
    //     sh 'git clone https://github.com/artem-pvl/devops_hw_11.git ./conf'
    //   }
    //   post{
    //       failure{
    //           echo "========A execution failed========"
    //       }
    //   }
    // }
    stage('Build app image and push to nexus') {
      agent {
        docker {
          alwaysPull true
          reuseNode true
          args '-u 0:0'
          // args '-u 0:0 -v /root/.ssh/id_rsa:/root/.ssh/id_rsa'
          image 'nexus:8123/buildserver:latest'
        }
      }
      steps {
        // sh 'rm -rf ./conf'
        // sh 'git clone https://github.com/artem-pvl/devops_hw_11.git ./conf'
        git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        withMaven {
          sh 'mvn package'
        }
        script {
          docker.withRegistry('http://nexus:8123', '6b2d0b83-9cca-4d23-b69b-bcf247bc8379') {
            sh 'cp ./target/hello-1.0.war ./conf/prod'
            sh 'docker build --tag prodserver ./conf/prod'
            sh 'docker tag prodserver nexus:8123/prodserver:latest'
            docker.image('prodserver').push('latest')
            sh 'docker rmi -f nexus:8123/prodserver:latest'
          }
        }
      }   
    }
    stage('Run prod docker container on node-1') {
      steps {
        sh 'pwd'
        sh 'rsync ./prod/docker-compose.yml root@node-1:./'
        sh 'ssh root@node-1 docker-compose up -d'
            // && docker run -d --rm --name prodserver -p 80:8080 nexus:8123/prodserver:latest'
        // sh 'touch ~/.ssh/known_hosts'
        // sh 'ssh-keyscan -H node-1 >> ~/.ssh/known_hosts'
        // sh 'ssh root@node-1 << EOF'
        // sh 'EOF'
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
