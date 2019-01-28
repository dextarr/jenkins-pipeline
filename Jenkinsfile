node {
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/dextarr/fibonacci-service-spring-boot/'
      echo "cwd: " + pwd()
   }
   stage('Build') {
      dir('build/test-results') {
         deleteDir()
      }
      sh "gradle build"
   }
   stage('Results') {
      junit '**/test-results/test/TEST-*.xml'
      archiveArtifacts 'build/libs/*.jar'
   }
   stage('Build_docker') {
      sh 'docker build -t qwepoi123/fibonacci-service-spring-boot .'
   }
   stage('Push_docker') {
      sh 'docker push qwepoi123/fibonacci-service-spring-boot:latest'
   }
   stage('Deploy') {
      sh '''docker pull qwepoi123/fibonacci-service-spring-boot'''
      sh '''set +e
            docker ps -af name=fibo | grep -w fibo
            if [ $? -eq 0 ]
            then
                echo "Clean up previous docker"
                docker stop fibo; docker rm fibo
            else
                echo "No container to be cleaned up"
            fi'''
      sh '''docker run -d -p 8888:8080 --name fibo qwepoi123/fibonacci-service-spring-boot'''
   }
   stage('Final check') {
      sleep 10
      sh '''curl -ikv https://localhost/fibonacci/5 | grep -F '{"id":1,"value":"0 1 1 2 3"}' '''
   }
}
